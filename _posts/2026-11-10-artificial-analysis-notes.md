---
layout: post
title: "Artificial Analysis：从跑分到生意"
date: 2026-11-10 12:00:00 -0500
categories: [llm, evals]
---

本文由 ChatGPT 根据我们的多轮讨论，在我的指导下整理成文。资料截至 2026 年 11 月 10 日。

## AA 比厂商自报多做了什么

AA Intelligence Index v5.0.1 将九项评测汇成一个分数，知识工作、编程、科学和通识各占 25%。它以英语文本能力为主，部分任务含图像，不能代表完整的多模态能力。[方法](https://artificialanalysis.ai/methodology/intelligence-benchmarking)

厂商发布表围绕新品展示能力，数字可能来自不同的测试来源、配置和预算。AA 按自己公布的协议重新运行多个厂商的模型，提供共同参照。两边即使用了同名 benchmark，也未必在做同一个实验。

**AA 的增量价值在持续测量：**适配模型、维护运行环境、处理失败、核对评分，再把能力、成本和速度放在一起。总分只是入口，背后的工作降低了买方逐家核对厂商自报的成本。

这也解释了它的生意：买 token 的企业购买选型依据，卖 token 的公司购买差距诊断。公开榜单建立信誉，数据订阅和定制评测将信誉变现。它有很强的研究咨询属性，也在通过 Optima 把评测做成软件。[创始人访谈](https://latent.space/p/artificialanalysis/)、[Optima](https://artificialanalysis.ai/optima)

## 它的局限与风险

**可比，不等于贴近实际。** Opus 5.5 在 AA 的 Terminal-Bench 4.0 中，用 mini-swe-agent 得到 59.6%，用 Claude Code 则约为 63%；两边都标为 max、default fallback。独立实验不能证明分差由 harness 导致，但足以说明：模型名字不是完整的测试对象。[模型结果](https://artificialanalysis.ai/models/comparisons/claude-opus-5-5-vs-gpt-6-astra)、[Agent 结果](https://artificialanalysis.ai/agents/coding-agents/comparisons/codex-vs-claude-code)

固定 harness 有利于比较模型，原生 agent 更接近产品体验。统一配置不能自动回答用户该买什么；综合分数也会掩盖任务差异和权重选择。

**方法公开，不等于结果可审计。** GDP.pdf 有公开数据与代码，AA 也展示部分 criterion-level 判断。但我没有找到足以审计任意模型分差的完整逐题输出与判分记录。公开 rubric，不等于公开 AA 的完整判分。保密题集可以防污染，缺少实验记录则限制外部验证，这是两件事。[GDP.pdf](https://artificialanalysis.ai/evaluations/gdp-pdf)

**被广泛引用，不等于形成付费依赖。** AA 已有品牌和持续测量的优势，更深的客户锁定仍待证明。企业若只用榜单初筛，再用内部任务做决定，流量就未必能变成稳定收入。

同时，被评公司也可能购买服务。AA 承诺商业关系不影响结果；这让利益边界、方法变更和可核验记录直接关系到它的商业资产：信任。[独立性承诺](https://artificialanalysis.ai/careers/member-of-technical-staff-commercial-tech-start-up-track)

它的长期生意依赖三个条件：模型持续存在有意义的差异，选错足够昂贵，独立测量能带来值得付费的决策收益。内部评测或平台附送工具若已足够，AA 就需要重新证明自己的价值。

衡量它的壁垒，我更想看：**客户是否因为这些比较反复改善了部署决策而续费，而不只是继续关注谁排第一。**

---

**English summary**

AA creates a common reference by rerunning models under a documented protocol and comparing capability, cost and speed. That improves on assembling vendor-reported numbers.

The limits matter: shared harnesses may miss product-level performance; public methods do not expose every underlying judgment; and selling services to evaluated vendors makes independence an ongoing obligation.

Its business needs meaningful model differences, costly mistakes and customers willing to pay for independent measurement. Industry references show visibility. A deeper moat requires customers to keep using AA because its comparisons repeatedly improve their decisions.
