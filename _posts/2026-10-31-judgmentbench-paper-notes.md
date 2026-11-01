---
layout: post
title: "Paper notes: JudgmentBench, pairwise preference vs. rubric scoring"
date: 2026-10-31 12:00:00 -0700
categories: paper-reading
---

Paper: [JudgmentBench: Comparing Rubric and Preference Evaluation for Quality Assessment](https://arxiv.org/abs/2605.25240v3) (Yang et al., 2026).

**My take:** the paper finds pairwise evaluation better and cheaper than rubrics. But its quality differences are **synthetically prompted**, so they may not reflect real quality differences and may favor pairwise. The accuracy result **may not generalize**.

## What they did

They used one model (Claude Opus 4.6) to answer 30 real legal tasks at **three prompted quality levels**. 51 attorneys then either scored the answers against an expert rubric or compared them in pairs.

| | Pairwise | Rubric |
|---|---:|---:|
| Spearman with intended ranking, pooled per task | 0.908 | 0.150 |
| Win rate per individual judgment | 0.669 | 0.542 |
| Median time per evaluation | 1.92 min | 4.74 min |

Note that a **single pairwise judgment** is right only about two-thirds of the time. The 0.908 comes from **pooling** many attorneys.

## What I questioned

**Are the quality differences meaningful?** A system prompt sets six dimensions, such as depth and precision, to the same level. The authors call this their **most significant limitation**: real work is mixed, and prompts may leave **stylistic cues**. Validation is thin too: one senior attorney's review, plus GPT-5.4 preferring the higher level about 68% of the time. GPT-5.4 was itself a pairwise judge, so that isn't neutral evidence.

**Is the comparison fair?** The rubrics weren't written for this manipulation, and the appendix example is largely a **content checklist**. Rubrics ranked excellent above intermediate on only 57% of tasks, against 97% for pairwise. Either the rubrics are weak, or the levels differ in things they don't measure. The design also **changes two things at once**: pairwise raters compared two answers with no rubric, while rubric raters scored one answer at a time. So it can't tell whether the gain comes from comparing or from dropping the rubric.

**Do the known trade-offs show up?** Mostly not. Interpretability and bias are discussed, not measured, and mixed-quality cases, where rubrics might do better, are untested.

## The basic question for follow-up

The paper measures recovery of a **prompted label**, and the authors say no method-independent ground truth exists. So recovering the label doesn't yet mean measuring quality. The question I'd pursue is: **do these methods predict something that actually matters?**

1. **Define the outcome upfront** for a concrete use case, such as editing time and missed errors for a contract-review memo.
2. **Use realistic outputs** from different models, with a few planted errors as a sanity check.
3. **Measure judgments and outcomes separately**, with different people.
4. **Test which method predicts the outcome**, and whether style-only rewrites change the scores.

The hard part is that outcomes can be **noisy, expensive and workflow-specific**, so I'd start with a **small pilot**. Reanalyzing the public data, separating the pairwise and rubric effects, and testing mixed-quality outputs are useful but secondary.
