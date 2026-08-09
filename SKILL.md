---
name: trader
description: "趋势交易者技能库（superpower 路由层）：为 AI Agent 提供美股日线趋势交易方法论，涵盖交易系统核心（三层过滤、仓位风险公式、入场退出）、持仓与期权决策框架，以及交易复盘。适用场景：评估个股入场机会、计算仓位与止损、管理持仓与候选池、评估 Bull Call Spread / Long Call 期权策略、单笔交易复盘与月度系统性回顾。核心理念：不预测，只跟随；先控制风险，再等待趋势。仅做多美股正股/ETF，不使用融资、不做日内交易。"
agent_created: true
metadata:
  version: 1.0.0
  category: 交易/投资
  difficulty: 专家
  architecture: superpower
  platform: skills-repo
  created: 2026-08-09
  updated: 2026-08-09
tags:
  - 美股
  - 趋势交易
  - 风险控制
  - 交易复盘
  - 期权策略
  - 仓位管理
---

# trader — 趋势交易者技能库

> AI Agent 日线趋势交易方法论 | 风险控制 | 复盘决策。本文件为超级技能（superpower）路由层，仅做索引；具体方法论按需读取 `skills/` 子技能与 `references/` playbook。

## 能力路由（渐进式加载）

| 任务 | 子技能 | 关键词（grep 线索） |
|------|--------|---------------------|
| 评估个股入场 / 大盘板块过滤 / 仓位风险公式 | `skills/trade-system` | 三层过滤, 20MA, 突破, 回踩, 仓位公式, 止损, 入场, 退出 |
| 持仓管理 / 候选标的池 / 期权决策边界 | `skills/trade-decisions` | 持仓快照, 候选池, Bull Call Spread, Long Call, 相关性, 加仓, 组合风险 |
| 单笔复盘 / R 值 / 月度统计 / 错误分类 | `skills/trade-review` | R值, 复盘, 期望值, 盈亏比, 最大回撤, 执行评估, 错误类型 |

> 完整决策演示见 [demo/example-trade.md](demo/example-trade.md)。

## 适用场景

- 用户要求评估某美股标的的入场机会或是否应该买入/卖出
- 用户要求计算仓位大小、初始止损与组合风险暴露
- 用户要求检查持仓是否符合三层过滤与交易边界
- 用户要求维护趋势小账户持仓快照、候选标的池或评估期权策略
- 用户完成一笔交易后要求复盘，或要求进行 20 笔 / 月度系统性统计
- 用户要求做一笔完整交易计划（系统评估 + 持仓决策 + 事后记录）

## 限制

- 只做多美股正股或 ETF；不做空、不使用融资、不做日内交易
- 期权仅限 Bull Call Spread 与 Long Call；不裸卖、不做 0DTE、不买深度虚值彩票 Call
- 单笔风险 0.5%–1% 账户净值；组合总风险（相关标的合并）≤ 3% 账户净值
- 收盘价后决策，盘中不临时改逻辑；连续亏损 / 取消止损 / 亏损加仓须当日停止交易
- 本仓库内容仅供学习参考，不构成投资建议；实际交易以券商与个人风险承受能力为准

## 参考手册

- `references/trade-decision-framework.md` — 入场决策矩阵、仓位公式与期权决策树
- `references/trade-review-checklist.md` — R 值计算、月度复盘模板与错误分类清单
