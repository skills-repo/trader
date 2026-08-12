# trader — 趋势交易者技能库

> AI Agent 日线趋势交易方法论 | 风险控制 | 复盘决策

## 定位

为 Claude Code / ClawHub 生态提供趋势交易相关的 AI Agent 技能。基于用户 15 年金融 IT + 美股交易实战经验，将日线趋势交易系统提炼为 Agent 可加载、可执行的 skill。

**核心理念：** 不预测，只跟随；先控制风险，再等待趋势。

## 安装

本仓库支持整库安装与按子技能单独安装（路径长期稳定、不会改名）：

```bash
# 整库安装
npx skills add skills-repo/trader

# 按子技能安装
npx skills add skills-repo/trader@trade-decisions
npx skills add skills-repo/trader@trade-review
npx skills add skills-repo/trader@trade-system
npx skills add skills-repo/trader@crypto-trading
npx skills add skills-repo/trader@quant-research
```

## 技能清单

| 技能 | 文件 | 说明 | 来源 |
|------|------|------|------|
| 交易系统核心 | [skills/trade-system/SKILL.md](skills/trade-system/SKILL.md) | 三层过滤、入场模式、仓位公式、退出规则 | 原创 |
| 交易决策框架 | [skills/trade-decisions/SKILL.md](skills/trade-decisions/SKILL.md) | 持仓管理、候选标的池、期权边界 | 原创 |
| 交易复盘 | [skills/trade-review/SKILL.md](skills/trade-review/SKILL.md) | R 值计算、错误分类、月度复盘模板 | 原创 |
| 加密货币研究与决策 | [skills/crypto-trading/SKILL.md](skills/crypto-trading/SKILL.md) | 链上数据解读、市场结构、现货仓位纪律 | 派生 |
| 量化研究与回测 | [skills/quant-research/SKILL.md](skills/quant-research/SKILL.md) | 回测验证、过拟合识别、因子边缘检验、参数高原 | 派生 |

## 子技能表

| 子技能 | 路径 | 适用 |
|--------|------|------|
| trade-system | `skills/trade-system` | 大盘/板块/个股三层过滤，仓位风险公式，入场退出决策 |
| trade-decisions | `skills/trade-decisions` | 持仓快照、候选标的池、期权策略与相关性风险管理 |
| trade-review | `skills/trade-review` | 单笔 R 值复盘、错误分类、20 笔/月度系统性回顾 |
| crypto-trading | `skills/crypto-trading` | 链上指标、稳定币背景、现货仓位与加密特有风险（不杠杆/不合约/不签名） |
| quant-research | `skills/quant-research` | 回测设计、样本外检验、过拟合红线、与趋势系统结合（不杠杆/不自动交易） |

## 演示

| 用例 | 说明 |
|------|------|
| [完整交易决策演示](demo/example-trade.md) | 从大盘评估到盈亏记录的全流程 |

使用方式：先阅读 [demo/example-trade.md](demo/example-trade.md) 了解一笔完整决策如何串联三层过滤、仓位计算与事后 R 值记录，再加载对应子技能实践。

## 交易系统来源

本仓库技能源于 [one](https://github.com/Linux2010/one) — 个人人生规划系统中的 trade 模块，包含完整的日线趋势交易方法论、投资日志和决策记录。加密货币研究与决策（`crypto-trading`）为独立扩展域，派生自社区技能 kukapay/crypto-skills@trading-strategist，沿用同源风险控制哲学但针对 24/7、无熔断、高波动市场单独设定纪律（现货、不杠杆、不合约、不链上签名）。

关键规则速览：

- **方向**：只做多美股正股或 ETF；不做空，不使用融资
- **周期**：周线判断背景，日线产生信号，收盘后决策
- **标的**：流动性好的强势标的，3–5 个主要风险仓
- **风险**：单笔 0.5%–1% 账户风险，相关标的合并计算
- **入场**：只做有效突破和上升趋势回踩后转强
- **退出**：初始止损处理判断错误，移动退出保护趋势利润

## 使用方式

Agent 根据任务加载对应 skill：

```
交易前评估  → trade-system + trade-decisions
持仓管理    → trade-decisions
交易后复盘  → trade-review
加密研究    → crypto-trading + references/crypto-research-playbook.md
量化研究    → quant-research + references/quant-research-playbook.md
完整决策    → 全部加载 + demo/example-trade.md
```

## 免责声明

本仓库内容仅供学习和参考，不构成投资建议。交易有风险，过往表现不代表未来收益。融资可能带来超过本金的损失。使用本仓库技能做出的交易决策由用户自行承担风险。

---

> 交易者思维：不预测，只跟随；先控制风险，再等待趋势。
