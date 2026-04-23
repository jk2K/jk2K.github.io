---
title: ai 编程模式分析
date: 2026-04-20 16:00:00 +0800
categories: [技术]
mermaid: true
tags:
- ai
---

## 目标

用 ai 技术自动化执行层。即理想情况下，从提出一个想法到想法在功能层面的上线，全程利用 ai 自动化完成



![image](/assets/img/ai-code-analysis/-9794AX11-IO6cEME45gg012WO3-R14PPFa64Coeg_4.png)

上图是一个需求从提出到上线到观测线上运行效果所经历的生命周期



## 传统协作方式

- 产品经理写需求
- 研发人工编写代码，人工评审代码，利用工具构建出产物
- 测试进行人工测试或者自动化测试
- 版本经理创建新版本
- 研发部署环境和运维。运维协助
- 工具负责监控，研发或者运维及时处理告警



## ai 编程在业界的实践

### 通过编写 skills 落地 ai 研发工作流

在 claude code 的基础上维护 skills 落地 ai 研发工作流

 [https://github.com/garrytan/gstack](https://github.com/garrytan/gstack)

| Skill（技能）                    | Your specialist（专家）         | What they do（专家能做什么？）                                                                                                                                                                                                                                                                                                                    |
|--------------------------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ``` /office-hours ```          | **YC Office Hours**           | Start here. Six forcing questions that reframe your product before you write code. Pushes back on your framing, challenges premises, generates implementation alternatives. Design doc feeds into every downstream skill.                                                                                                                      |
| ``` /plan-ceo-review ```       | **CEO / Founder**             | Rethink the problem. Find the 10-star product hiding inside the request. Four modes: Expansion, Selective Expansion, Hold Scope, Reduction.                                                                                                                                                                                                    |
| ``` /plan-eng-review ```       | **Eng Manager**               | Lock in architecture, data flow, diagrams, edge cases, and tests. Forces hidden assumptions into the open.                                                                                                                                                                                                                                     |
| ``` /plan-design-review ```    | **Senior Designer**           | Rates each design dimension 0-10, explains what a 10 looks like, then edits the plan to get there. AI Slop detection. Interactive — one AskUserQuestion per design choice.                                                                                                                                                                     |
| ``` /plan-devex-review ```     | **Developer Experience Lead** | Interactive DX review: explores developer personas, benchmarks against competitors' TTHW, designs your magical moment, traces friction points step by step. Three modes: DX EXPANSION, DX POLISH, DX TRIAGE. 20-45 forcing questions.                                                                                                          |
| ``` /design-consultation ```   | **Design Partner**            | Build a complete design system from scratch. Researches the landscape, proposes creative risks, generates realistic product mockups.                                                                                                                                                                                                           |
| ``` /review ```                | **Staff Engineer**            | Find the bugs that pass CI but blow up in production. Auto-fixes the obvious ones. Flags completeness gaps.                                                                                                                                                                                                                                    |
| ``` /investigate ```           | **Debugger**                  | Systematic root-cause debugging. Iron Law: no fixes without investigation. Traces data flow, tests hypotheses, stops after 3 failed fixes.                                                                                                                                                                                                     |
| ``` /design-review ```         | **Designer Who Codes**        | Same audit as /plan-design-review, then fixes what it finds. Atomic commits, before/after screenshots.                                                                                                                                                                                                                                         |
| ``` /devex-review ```          | **DX Tester**                 | Live developer experience audit. Actually tests your onboarding: navigates docs, tries the getting started flow, times TTHW, screenshots errors. Compares against `/plan-devex-review` scores — the boomerang that shows if your plan matched reality.                                                                                         |
| ``` /design-shotgun ```        | **Design Explorer**           | "Show me options." Generates 4-6 AI mockup variants, opens a comparison board in your browser, collects your feedback, and iterates. Taste memory learns what you like. Repeat until you love something, then hand it to `/design-html`.                                                                                                       |
| ``` /design-html ```           | **Design Engineer**           | Turn a mockup into production HTML that actually works. Pretext computed layout: text reflows, heights adjust, layouts are dynamic. 30KB, zero deps. Detects React/Svelte/Vue. Smart API routing per design type (landing page vs dashboard vs form). The output is shippable, not a demo.                                                     |
| ``` /qa ```                    | **QA Lead**                   | Test your app, find bugs, fix them with atomic commits, re-verify. Auto-generates regression tests for every fix.                                                                                                                                                                                                                              |
| ``` /qa-only ```               | **QA Reporter**               | Same methodology as /qa but report only. Pure bug report without code changes.                                                                                                                                                                                                                                                                 |
| ``` /pair-agent ```            | **Multi-Agent Coordinator**   | Share your browser with any AI agent. One command, one paste, connected. Works with OpenClaw, Hermes, Codex, Cursor, or anything that can curl. Each agent gets its own tab. Auto-launches headed mode so you watch everything. Auto-starts ngrok tunnel for remote agents. Scoped tokens, tab isolation, rate limiting, activity attribution. |
| ``` /cso ```                   | **Chief Security Officer**    | OWASP Top 10 + STRIDE threat model. Zero-noise: 17 false positive exclusions, 8/10+ confidence gate, independent finding verification. Each finding includes a concrete exploit scenario.                                                                                                                                                      |
| ``` /ship ```                  | **Release Engineer**          | Sync main, run tests, audit coverage, push, open PR. Bootstraps test frameworks if you don't have one.                                                                                                                                                                                                                                         |
| ``` /land-and-deploy ```       | **Release Engineer**          | Merge the PR, wait for CI and deploy, verify production health. One command from "approved" to "verified in production."                                                                                                                                                                                                                       |
| ``` /canary ```                | **SRE**                       | Post-deploy monitoring loop. Watches for console errors, performance regressions, and page failures.                                                                                                                                                                                                                                           |
| ``` /benchmark ```             | **Performance Engineer**      | Baseline page load times, Core Web Vitals, and resource sizes. Compare before/after on every PR.                                                                                                                                                                                                                                               |
| ``` /document-release ```      | **Technical Writer**          | Update all project docs to match what you just shipped. Catches stale READMEs automatically.                                                                                                                                                                                                                                                   |
| ``` /retro ```                 | **Eng Manager**               | Team-aware weekly retro. Per-person breakdowns, shipping streaks, test health trends, growth opportunities. `/retro global` runs across all your projects and AI tools (Claude Code, Codex, Gemini).                                                                                                                                           |
| ``` /browse ```                | **QA Engineer**               | Give the agent eyes. Real Chromium browser, real clicks, real screenshots. ~100ms per command. `/open-gstack-browser` launches GStack Browser with sidebar, anti-bot stealth, and auto model routing.                                                                                                                                          |
| ``` /setup-browser-cookies ``` | **Session Manager**           | Import cookies from your real browser (Chrome, Arc, Brave, Edge) into the headless session. Test authenticated pages.                                                                                                                                                                                                                          |
| ``` /autoplan ```              | **Review Pipeline**           | One command, fully reviewed plan. Runs CEO → design → eng review automatically with encoded decision principles. Surfaces only taste decisions for your approval.                                                                                                                                                                              |
| ``` /learn ```                 | **Memory**                    | Manage what gstack learned across sessions. Review, search, prune, and export project-specific patterns, pitfalls, and preferences. Learnings compound across sessions so gstack gets smarter on your codebase over time.                                                                                                                      |



当前问题：AI 需求完成度不够高，还需要人工去做很多事情。要想提高 AI 需求完成度，需要解决以下两个问题

- 需求怎么更清晰完整，原型怎么更好理解
- 怎么自动化验证。需要让 ai 自我验证，验证不通过，继续开发，直至需求真的开发完成了，否则还需要人在过程中监督、反复检查



### 数字员工

#### hermes-agent

 [https://github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)



#### qwibitai/nanoclaw

 [https://github.com/qwibitai/nanoclaw](https://github.com/qwibitai/nanoclaw)



#### openclaw/openclaw

 [https://github.com/openclaw/openclaw](https://github.com/openclaw/openclaw)



#### Claude Code

 [https://code.claude.com/docs/en/channels-reference](https://code.claude.com/docs/en/channels-reference)



### 数字公司

#### paperclipai/paperclip

 [https://github.com/paperclipai/paperclip](https://github.com/paperclipai/paperclip)



{% include embed/video.html src='/assets/img/ai-code-analysis/ghTPuGBP-jnyFQBE5WtlKYXh9BSBxnAj7pKnlTKW678.mp4' %}





#### multica-ai/multica

 [https://github.com/multica-ai/multica](https://github.com/multica-ai/multica)



### 工具

#### 好的大模型

gpt 5.4 / opus 4.7 / Gemini 3.1 Pro



opus 4.7 最强，但中国不可用



token 额度。 codex和claude code 编程套餐(max)，一个月 200刀



#### 需求梳理 / 编码

两大工具 codex 和 claude code

 [https://www.npmjs.com/package/@openai/codex](https://www.npmjs.com/package/@openai/codex) 

 [https://www.npmjs.com/package/@anthropic-ai/claude-code](https://www.npmjs.com/package/@anthropic-ai/claude-code)



搭配合适的 skills，编码工具也可处理需求梳理工作



#### 产品原型制作

需求在描述过程中，可以通过做产品原型来减少沟通成本

Claude Design

{% include embed/video.html src='/assets/img/ai-code-analysis/SQcaGbZoQ8yav-MIPMfXsrIi-oeJ1cbajjZgG3MpJTo.mp4' %}



## 开放问题

### OPC 模式（一人公司）

其他一些落地可能存在的问题

1. 组织结构，协作模式。不用分工太细了，一个人干很多角色的活；汇报层级也可能减少，很多数据都是ai可见的，那ai直接总结就好了，就不需要很多中间层了
2. 绩效考核。怎么激励大家往这种方式发展
3. 好的工程实践。例如 驾驭工程（harness engineering）的不断发展，能够不断的提升 ai 任务完成度和质量。怎么把控质量？可能解法：用ai把控ai，想办法给 ai 注入更多专业性或者设计品味
4. 好的工具。例如 claude design / claude code 能够被不断引入
5. 算力 + 好的大模型。中国的模型跟美国模型还是有差距，模型能力差一点，agent 调用成百上千次后，误差可能放大








