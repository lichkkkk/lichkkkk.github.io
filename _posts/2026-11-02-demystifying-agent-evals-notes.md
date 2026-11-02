---
layout: post
title: "Reading notes: Demystifying evals for AI agents"
date: 2026-11-02 12:00:00 -0700
categories: paper-reading
---

Article: [Demystifying evals for AI agents](https://www.anthropic.com/research/demystifying-evals-for-ai-agents) (Anthropic, 2026).

## Notes

- **Components of an agent eval:** task, trial, grader, transcript, outcome, eval harness, agent harness, suite.
  - Evaluating an agent means evaluating **model + agent harness** together.
  - The outcome is the **final environment state** (is the reservation in the DB?), not what the agent says.
- **Graders:** code, model, human. Each covers some cases.
  - Priority: code first, LLM judge where needed, humans to calibrate.
  - LLM judges: give them an "Unknown" option, use one judge per dimension, give partial credit, make graders hack-resistant.
- **Capability vs. regression evals:** capability evals start at a low pass rate (room to climb). Regression evals sit near 100%. Saturated capability tasks graduate into regression.
- **What to grade:** outcomes, not rigid paths. On τ2-bench, Opus 4.5 "failed" by finding a policy loophole that was better for the user. Use trajectory checks for constraints such as turn limits and required tool calls.
- **Agent types:** coding, conversational, (deep) research, computer use.
- **Non-determinism:**
  - pass@k (≥1 success in k) for when one success is enough.
  - pass^k (all k succeed) for reliability. 0.75³ ≈ 42%.
- **Building evals from 0 to 1:**
  - Early stage: changes have large effects, so **20–50 simple real tasks** are enough.
  - Data: real use cases, unambiguous tasks with reference solutions. **0% pass usually means a broken task.** Balanced means testing both when a behavior should and shouldn't happen.
  - Eval harness: stable, matches serving, a **clean environment per trial** (leaked git history inflated scores).
  - Grader: outcome + trajectory, calibrated. Eval bugs look like agent failures: CORE-Bench went 42% → 95% after fixes.
  - Maintenance: **read transcripts**, watch saturation (SWE-bench Verified ~30% → 80%+), refresh tasks.
- **Swiss cheese model:** automated evals, A/B testing, online monitoring, manual review, user feedback and human raters. No single layer catches everything.

## Turning this into a frontier-eval playbook

The article is written for a product team: one agent, one decision (is it ready to ship?). Using it as a playbook for frontier model evals needs four main changes:

- **Tasks: production failures plus forward-looking tasks.** Real failures are still a strong source, but each one needs **attribution** (is it the weights, or the harness/spec/context?) and **generalization** into a task family. Production also trails the frontier because users stop asking for what fails, so expert-written tasks that look ahead are needed too.
- **Difficulty: hard tasks need audits.** Filtering for difficulty also selects for broken tasks. OpenAI found flawed tests in 59.4% of the SWE-bench Verified problems models often failed.
- **Graders: verifiable first, narrow LLM judges, separate from training.** Headline capability scores mostly rely on execution or outcome checks (tests, final state) or on human preference. A holistic LLM judge is a weak fit here: it struggles to grade models stronger than itself, gets exploited under optimization when it works like a reward model, and is hard to reproduce (judges sample, drift between versions and get deprecated). When an LLM judge is used, narrow its job to something close to verifiable, such as answer matching or a single rubric item, pin its version and prompt, and report how often it agrees with humans. Keep graders, tasks and environments separate from RL, and control contamination.
- **Measurement: isolate the model, with statistics.** Differences between checkpoints are 1–3 points, so error bars and paired tests are needed. Fix the scaffold, compute and infrastructure, since infrastructure alone can move agentic scores by several points.

**Further reading:** [ABC checklist](https://arxiv.org/abs/2507.02825) · [OpenAI on retiring SWE-bench Verified](https://openai.com/index/why-we-no-longer-evaluate-swe-bench-verified/) · [Adding Error Bars to Evals](https://arxiv.org/abs/2411.00640) · [Infrastructure noise in agentic evals](https://www.anthropic.com/engineering/infrastructure-noise) · [The Leaderboard Illusion](https://arxiv.org/abs/2504.20879)
