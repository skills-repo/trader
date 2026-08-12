# AGENTS.md

## 仓库性质

这是一个 **AI Agent 技能库**（trader 趋势交易技能），不是软件项目。所有内容为 Markdown 格式的技能定义文件，采用 superpower 五层架构（Level B）。

## 目录约定

```
trader/
├── README.md              # 项目介绍和使用指南
├── SKILL.md               # 超级技能路由层（L1，根入口）
├── AGENTS.md              # AI 助手使用指引（本文件）
├── LICENSE                # MIT 许可证
├── .gitignore
├── demo/
│   └── example-trade.md   # 完整交易决策演示
├── skills/                # 子技能目录（L3）
│   ├── trade-system/      # 日线趋势交易核心系统
│   │   └── SKILL.md
│   ├── trade-decisions/   # 持仓管理与决策框架
│   │   └── SKILL.md
│   └── trade-review/      # 交易复盘技能
│       └── SKILL.md
│   └── crypto-trading/    # 加密货币研究与决策框架
│       └── SKILL.md
└── references/            # 参考手册（L2，按需读取）
    ├── trade-decision-framework.md
    ├── trade-review-checklist.md
    └── crypto-research-playbook.md
```

## 工作约定

- 所有技能内容使用中文编写
- 面向趋势交易者，假设其理解美股、20MA、R 值等基础概念
- 输出可执行的判断与公式（仓位计算、R 值、期望值），不是空泛理论
- 严格风险控制：美股单笔 0.5%–1%、组合 ≤ 3% 账户净值；只做多美股正股/ETF
- 加密货币为独立扩展域：现货、不杠杆、不合约、不链上签名；单笔 1%–2%、组合 ≤ 3%–5%；链上研究优先于图表

## 技能添加流程

1. 在 `skills/` 下创建以技能名（小写-连字符）命名的目录
2. 编写 `skills/<name>/SKILL.md`，含 frontmatter（name/description/metadata.architecture: superpower）
3. 在根 `SKILL.md` 路由表与 `README.md` 子技能表同步登记
4. 如需完整方法论，补充 `references/` playbook

## 不做什么

- 不做空、不使用融资、不做日内交易
- 不输出无风险公式的"建议买入"结论
- 不构成投资建议；实际交易以券商与个人风险承受能力为准
