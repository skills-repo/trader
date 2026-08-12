# 量化研究 Playbook

> 本手册是 `skills/quant-research` 的可执行补充。核心目标：把"这个系统/因子靠不靠谱"转成一张可逐步打勾、可复现、带过拟合检查的研究表。
> **只做研究，不执行任何交易；所有命令零密钥、可本地重跑。**

---

## 1. 研究总览树（从假设到落地）

```
明确可证伪假设
│  例："价格站上 20MA 且 50MA 向上时，未来 20 日收益显著为正"
│
├─ 数据：调整后收盘价，跨度含牛/熊/震荡（≥ 1 个完整周期）
├─ 拆分：前 70% 训练，后 30% 样本外（外样本全程不碰）
├─ 回测：固定成本，信号 T 收 / 成交 T+1 开，避免未来函数
├─ 检验：参数高原 + 分阶段（牛/熊/震荡）+ 去极端年
└─ 落地：稳健→置信增强（不改仓位公式）；不稳健→仅样本内，退回人工
```

执行顺序严格 **假设 → 数据 → 拆分 → 回测 → 检验 → 落地**；任一步不达标不打"已验证"标签。

---

## 2. 回测设计决策矩阵

| 维度 | 正确做法 | 错误做法（红线） | 后果 |
|------|----------|------------------|------|
| 数据复权 | 调整后收盘价（含分红拆股） | 未复权 / 混用 | 收益错位 |
| 样本拆分 | 训练 70% + 外样本 30% | 全量调参后报全量 | 过拟合自欺 |
| 成交假设 | T 收信号 / T+1 开成交 | 当日收盘即用收盘价成交 | 未来函数 |
| 成本 | 每笔手续费+滑点（如 0.1%+0.05%） | 零成本 | 收益虚高 30%+ |
| 参数 | 看"参数高原"范围 | 只挑夏普最高的单点 | 尖峰必过拟合 |
| 生存偏差 | 含已退市标的的宽基指数 | 只用当前存活成分 | 自动过滤失败者，虚高 |

**判定规则**：外样本为正且参数高原平坦，才标"已验证"；外样本转负 → 降级"仅样本内"。

---

## 3. 命令（开源、零密钥）

> 优先用 pandas 自包含脚本（无需 API key）；需要向量化回测可装 `vectorbt`（社区 2.3K 安装）。

### 3.1 数据读取（本地 CSV，列：Date,Close）

```python
import pandas as pd

df = pd.read_csv("prices.csv", parse_dates=["Date"]).sort_values("Date")
df = df.set_index("Date")["Close"]          # 用调整后收盘价列
```

### 3.2 可复现的 MA 交叉回测（含成本与样本外）

```python
import numpy as np, pandas as pd

def ma_cross_backtest(close, fast=20, slow=50,
                      fee=0.001, split=0.7):
    """信号 T 收 / 成交 T+1 开；返回训练集与外样本绩效。"""
    ret = close.pct_change().fillna(0)
    fast_ma = close.rolling(fast).mean()
    slow_ma = close.rolling(slow).mean()
    # 持仓：快线在慢线上方 → 1，否则 0（T 收判定，T+1 才生效）
    pos = (fast_ma > slow_ma).astype(int).shift(1).fillna(0)
    strat = pos * ret - fee * pos.diff().abs().fillna(0)  # 换手成本
    n = int(len(strat) * split)
    train = strat.iloc[:n]
    oos   = strat.iloc[n:]
    def stats(s):
        equity = (1 + s).cumprod()
        peak = equity.cummax()
        mdd = (equity / peak - 1).min()
        return {"total_return": float(equity.iloc[-1] - 1),
                "win_rate": float((s > 0).mean()),
                "max_drawdown": float(mdd)}
    return {"train": stats(train), "oos": stats(oos)}

# 示例（需先准备 prices.csv）
# print(ma_cross_backtest(df))
# {'train': {total_return:0.82, win_rate:0.56, max_drawdown:-0.21},
#  'oos':   {total_return:0.11, win_rate:0.51, max_drawdown:-0.18}}
```

> 解读铁律：若 `oos.total_return` 远低于 `train.total_return`，策略大概率是过拟合——外样本才是"实盘代理"。

### 3.3 参数高原扫描（拒绝单点最优）

```python
for f, s in [(10,30),(20,50),(50,100),(100,200)]:
    r = ma_cross_backtest(df, f, s)
    print(f"fast={f} slow={s} | train={r['train']['total_return']:.2f} "
          f"oos={r['oos']['total_return']:.2f}")
# 若多个相邻参数都为正且接近 → 参数高原（稳健）；仅 1 个尖峰 → 过拟合
```

### 3.4 vectorbt 向量化（可选，社区成熟）

```bash
pip install vectorbt
```

```python
import vectorbt as vbt
# 见 community: marketcalls/vectorbt-backtesting-skills@vectorbt-expert
pf = vbt.Portfolio.from_holding(close, direction="longonly")
print(pf.stats())
```

---

## 4. 过拟合红线清单（逐条打勾，命中即作废）

- [ ] 是否在参数网格里挑了单点最优（应是参数高原）
- [ ] 是否只报了样本内、未用样本外
- [ ] 是否含未来函数（用了当时不可见数据）
- [ ] 是否零成本（未计手续费+滑点）
- [ ] 是否仅用存活成分（生存偏差）
- [ ] 是否数据只覆盖牛市一段
- [ ] 是否因回测漂亮就计划加杠杆（禁止）
- [ ] 是否把回测胜率当成实盘保证（仅改置信度）

任一项命中 → 结论降级为"不可信 / 仅样本内"，不进入交易决策。

---

## 5. 与趋势系统结合的检查清单

- [ ] 回测对象是否就是 `trade-system` 的某条规则（如三层过滤中的突破）
- [ ] 外样本稳健 → 仅在三层过滤外加"历史置信增强"标注
- [ ] 仓位公式是否未变（仍按单笔风险 0.5%–1% 计算）
- [ ] 是否未因回测结果放大仓位或上杠杆
- [ ] 不稳健 → 是否退回 `trade-system` 人工判断，而非硬用

---

## 6. 输出模板（研究后回填）

```
## 假设
- [一句话可证伪假设]
## 数据
- 区间: [起]–[止]，含牛/熊/震荡: [是/否]，复权: [调整后]
## 设计
- 成本: 手续费 [x%] + 滑点 [x%]；成交: T收/T+1开；拆分: 训练[70%]/外[30%]
## 绩效
- 训练: 总收益 [x] 胜率 [x] 最大回撤 [x]
- 外样本: 总收益 [x] 胜率 [x] 最大回撤 [x]
## 参数高原
- [多个相邻参数均正 → 稳健 / 仅单点尖峰 → 过拟合]
## 分阶段
- 牛: [x] 熊: [x] 震荡: [x]（是否都不过度退化）
## 结论
[已验证/仅样本内/已证伪/待复核] — [原因]；对趋势系统影响: [置信增强/无/退回人工]
```
