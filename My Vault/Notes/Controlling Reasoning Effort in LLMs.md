
# Controlling Reasoning Effort in LLMs


DATE:  30-07-26


Tags:  [[Notes/Reasoning Models|Reasoning Models]] [[Notes/LLMs|LLMs]]

# References:
https://magazine.sebastianraschka.com/p/controlling-reasoning-effort-in-llms


# Content:

## 🎯 What Is "Reasoning Effort" (Reasoning Level)?

Reasoning models don't jump straight to an answer — they first generate an internal "thinking" trace (text between `<think>...</think>` tags) where they work through the problem step by step, before writing the final response. See [[Reasoning Models]] for the basics of this.

**Reasoning effort / reasoning level** = a dial that controls *how much thinking the model does* before answering — i.e., how many "thinking tokens" it spends.

- **High effort** → longer reasoning trace → usually more accurate → slower & more expensive
- **Low effort** → short/no reasoning trace → faster & cheaper → less accurate on hard problems

This gives two independent ways to make a model "smarter": make the model itself bigger/better (fixed at training time), or let it think longer at answer-time (adjustable per request). The latter is often called **test-time compute / inference-time scaling** — a separate lever from model size.

## 🏗️ How It's Implemented at Training Time

The model has to *learn* to associate some signal (a label, a flag, a number) with shorter or longer reasoning:

1. **Start with RL on verifiable problems (RLVR)** – Reinforcement Learning with Verifiable Rewards: train on math/code problems with a simple right/wrong (1/0) reward. Long, careful reasoning emerges naturally, even though the reasoning steps themselves aren't directly supervised.
2. **Add a length penalty to the reward** – To make effort *controllable*, a penalty based on reasoning length is subtracted from the reward. A stronger penalty trains a "low effort" mode (short reasoning); a weaker/no penalty trains a "high effort" mode (long reasoning).
3. **Fine-tune on effort-labeled examples (SFT)** – After RL, models are also fine-tuned on conversations explicitly tagged low/medium/high (or "thinking" vs "no thinking"), so the model learns to read a label and match its response length/style to it. (e.g. Qwen3's "Thinking Mode Fusion" mixes `/think` and `/no_think` examples this way.)
4. **Variant: train separate specialists, then merge** – Some labs train a distinct version of the model per effort level and then combine/distill them into one switchable model. (e.g. DeepSeek trains separate "Non-think / Think High / Think Max" specialists, then distills them together.)

**In short:** training bakes in the model's *ability* to change how much it reasons — it doesn't just reason the same amount by default.

## 🎛️ How It's Controlled at Inference Time

Once trained, effort is usually just *selected* per request — no retraining needed:

1. **A plain-language instruction** – literally telling the model "Reasoning effort: low / medium / high" in the system prompt. Works because the model was trained to follow it. (e.g. GPT-OSS models do exactly this.)
2. **An API / library flag** – a structured setting (not visible prompt text), like a `reasoning_effort` parameter or an `enable_thinking = True/False` switch, translated into special tokens by the library. (e.g. Qwen3's `enable_thinking` flag.)
3. **A hard token budget cutoff** – the system forcibly cuts off the reasoning trace after N tokens (regardless of whether the model "feels done"), then lets it write the final answer anyway. (e.g. Qwen3 and Nemotron support explicit token budgets like this.)
4. **Choosing model + effort together** – model families now often offer several model sizes *and* several effort levels, so you're really picking a point on a 2D grid (model scale × reasoning effort), not just one dial.

## ⚖️ Key Takeaways (for later deep-diving)

- **Reasoning effort ≈ how many "thinking" tokens a model spends before answering** — a dial trading cost/latency against accuracy.
- It's a **separate lever from model size**: "make the model bigger" (train-time) vs. "let it think longer" (inference-time) are two different ways to get better answers.
- **Training** = teaching the model to *respond* to an effort signal (RL length penalties + labeled fine-tuning). **Inference** = simply *choosing* that signal per request (prompt text, API flag, or hard cutoff).
- Returns diminish at the top — going from medium → high effort often costs a lot more tokens for a small accuracy gain.
- **No single standard implementation** yet — every lab (DeepSeek, Qwen, GLM, Kimi, Nemotron, GPT-OSS, etc.) wires up the training/plumbing slightly differently, but they all converge on the same basic idea above.
- Likely future direction: instead of users manually picking a level, agent systems may start **auto-selecting reasoning effort** based on task difficulty, with manual override only when needed.
