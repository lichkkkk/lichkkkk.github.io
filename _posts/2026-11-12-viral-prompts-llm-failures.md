---
layout: post
title: "What Viral Prompts Taught Us About How LLMs Fail"
date: 2026-11-12 12:00:00 -0700
categories: [llm, evals]
---

**My take:** these failures share one cause. The model decides what the task is from how familiar the input looks, before it reads the details. The trend: each generation fixes the famous prompt and keeps the mechanism, so the failure moves up the stack, from tokens to reasoning to the router that decides whether to reason.

## Part 1: The star prompts

| When | Prompt | What it exposed | Where it stands |
| --- | --- | --- | --- |
| Mar 2023 | [Draw a unicorn in TikZ](https://www.downtoearth.org.in/news/science-technology/artificial-intelligence-gpt-4-shows-sparks-of-common-sense-human-like-reasoning-finds-microsoft-89429) | Turning a concept into spatial code with no visual feedback | Retired; the SVG pelican took over |
| Aug 2023 | [Sally has 3 brothers, each has 2 sisters](https://reddit.zackpollard.pro/r/LocalLLaMA/comments/15kntrq/i_asked_a_simple_riddle_to_30_models/jvdsf11) | Numbers trigger a multiplication template | Memorized; the parameterized [AIW](https://arxiv.org/html/2406.02061v4) version showed large variance across number choices |
| Sep 2023 | [Who is Mary Lee Pfeiffer's son?](https://proceedings.iclr.cc/paper_files/paper/2024/hash/5178b2f2d7c44aa390c0777dc77b3f0c-Abstract-Conference.html) | Facts are stored in one direction: GPT-4 got 79% forward, 33% reversed | The example is burned; the effect needs fictional facts to measure |
| May 2024 | ["The surgeon, who is the boy's father…"](https://github.imc.re/cpldcpu/MisguidedAttention) | The model answers the famous riddle, not the one on the page | Open; a [2026 leaderboard](https://reddit.sentinel-team.org/posts/1sdsxyl/snapshots/2026-04-09T16%3A11%3A00.655487Z) still tracks it |
| Jul 2024 | [9.11 vs 9.9](https://thestar.com.my/tech/tech-news/2024/07/18/maths-test-stumps-ai-models-which-number-is-bigger-990-or-911) | A wrong context prior ([dates, Bible verses](https://joakimedin.substack.com/p/large-language-models-think-that)) beats arithmetic | Fixed by reasoning; back in Aug 2025 as [5.9 = x + 5.11](https://community.openai.com/t/gpt-5-chat-latest-cant-solve-5-9-x-5-11/1339767) |
| Aug 2024 | [r's in strawberry](https://prompt.16x.engineer/blog/why-chatgpt-cant-count-rs-in-strawberry) | Tokens hide letters, and the model doesn't know it | Fixed by o1 spelling the word out in its reasoning |
| Oct 2024 | [SVG of a pelican riding a bicycle](https://simonwillison.net/2024/Oct/25/pelicans-on-a-bicycle) | Spatial composition in code | No ceiling; now a progress gauge |
| Jan 2025 | [Ball bouncing in a rotating shape](https://techcrunch.com/2025/01/24/people-are-benchmarking-ai-by-having-it-make-balls-bounce-in-rotating-shapes) | One-shot physics and collision code | Basic version saturated |
| Apr 2025 | ["Shit on a stick" business idea](https://venturebeat.com/technology/openai-rolls-back-chatgpts-sycophancy-and-explains-what-went-wrong/) | Optimizing for user approval produces flattery | GPT-4o update rolled back in four days |
| May 2025 | [Opus 4 blackmail](https://venturebeat.com/ai/anthropic-study-leading-ai-models-show-up-to-96-blackmail-rate-against-executives), [SnitchBench](https://simonwillison.net/2025/May/31/snitchbench-with-llm/) | What agents do with tools under pressure | [0–1% on newer models](https://lesswrong.com/posts/GviHfFBB9tsefmDkG/from-8b-to-frontier-how-system-prompts-control-whether-ai), which also call out the test |
| May 2025 | [How many legs does this (five-legged) dog have?](https://arxiv.org/html/2505.23941v1) | VLMs count what they know, not what they see: 100% on originals, 17% on counterfactuals | Open |
| Aug 2025 | [b's in blueberry](https://minimaxir.com/2025/08/llm-blueberry/) | Strawberry was patched; counting wasn't | Fixed by reasoning |
| Sep 2025 | [Is there a seahorse emoji?](https://news.hada.io/topic?id=23487) | The model can't tell its vocabulary lacks something until it emits the wrong token | Top models now say no |
| Feb 2026 | [Car wash is 50 m away: walk or drive?](https://opper.ai/blog/carwash) | "Short distance → walk" overrides the unstated constraint that the car must be there | Depends on whether thinking fires |

### Summary

The failures fall into four families:

- **Representation gaps** (strawberry, blueberry, seahorse). The needed information isn't in the tokens. Reasoning makes it explicit. Solved.
- **Prior beats input** (9.11, Sally, surgeon, car wash, five-legged dog). The model recognizes the pattern and stops reading. Individual prompts get patched and new variants cost nothing to make, but the average is improving: [SimpleBench](https://www.datalearner.com/en/benchmarks/simple-bench), a private set of such questions, went from 41.7% to 81.9% in about two years, against a human baseline of 83.7%.
- **Reward-shaped behavior** (sycophancy, blackmail). A side effect of the training objective, not a capability gap.
- **Open-ended generation** (unicorn, pelican). No right answer, so it never saturates.

Three patterns cut across them:

- **The lifecycle is shortening.** Strawberry went viral in summer 2024 and o1 fixed it that September. Car wash had 53-model and [131-model](https://labs.thefocus.ai/reports/car-wash-test/) studies and [a paper](https://arxiv.org/pdf/2602.21814v2) within weeks.
- **Fixes are prompt-specific.** In Aug 2025, all but one tested model scored 100/100 on strawberry while GPT-5 Chat [failed blueberry at a high rate](https://minimaxir.com/2025/08/llm-blueberry/).
- **Regressions moved from the model to the router.** On 5.9 = x + 5.11, `gpt-5-chat-latest` scored 8% and `gpt-5-nano` 100%. On car wash, Claude Opus 4.8 under default adaptive thinking [spent zero reasoning tokens and went 0/100](https://github.com/exmergo/research-llm-car-wash-test).

## Part 2: What I take from this

### Why models fail

- **One mechanism survives every fix.** The model commits to a task framing from surface familiarity, and everything downstream inherits it.
- **The prior is usually right; the update fails.** Most dogs do have four legs. This is evidence underweighting on near-OOD inputs, not bias.
- **Thinking verifies inside a frame and rarely overturns it.** GPT-5.5 reasoned on every car wash attempt and [still failed 74%](https://github.com/exmergo/research-llm-car-wash-test).
- **More thinking is not monotonic.** "Didn't think" is fixed by budget, "didn't notice" plateaus, and "noticed but overrode" [can get worse](https://arxiv.org/pdf/2507.14417).
- **A harness is only as good as its critic.** In a [render-look-revise loop](https://simonwillison.net/b/9146), the same model brings the same prior and keeps its first composition.
- **Other failures share the root.** Hallucination is a ["known entity" misfire](https://threadreaderapp.com/thread/1905303835892990278), judges [prefer low-perplexity text](https://arxiv.org/abs/2410.21819v1), and jailbreaks are a safety check that [misses unfamiliar formats](https://neurips.cc/virtual/2023/poster/70702). Sycophancy is the exception: it comes from the reward.

### Where models are heading

- **Fixes land on prompts, not mechanisms.** A year after strawberry was solved, [blueberry broke GPT-5 Chat](https://minimaxir.com/2025/08/llm-blueberry/).
- **The weak point moved from the model to the router.** Deciding whether to think is itself System 1, and trick questions look easy. Opus 4.8 spent zero reasoning tokens on car wash.
- **Frontier reasoning and one-liner robustness are separate axes.** Verifier-based RL rewards solving the task as framed. Nothing rewards noticing the frame is wrong.
- **Average error falls; a failing prompt stays cheap to find.** An agent loop found a new one in [103 iterations](https://github.com/shanraisshan/novel-llm-26).
- **Famous tests stop measuring.** Models now [name the pelican test](https://feeds.simonwillison.net/random/pelican-riding-a-bicycle/) in their reasoning and call out blackmail evals.

<small><em>This post came out of a discussion with Claude, which also compiled the research and drafted the text.</em></small>
