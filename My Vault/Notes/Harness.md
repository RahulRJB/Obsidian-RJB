
# Harness


DATE:  16-04-26


Tags: [[Notes/Agentic AI|Agentic AI]]

# References:

https://blog.langchain.com/the-anatomy-of-an-agent-harness/


# Content:

### The Core Equation

**Agent = Model + Harness.** If you're not the model, you're the harness.

A **model** is the intelligence (takes in text/images, outputs text). A **harness** is everything else — the code and infrastructure that makes that intelligence actually useful.

This is the cleanest definition because it forces us to think about **designing systems around model intelligence.**

---

### What's in a Harness?

A harness includes: 
1. System Prompts, 
2. Tools/MCPs and their descriptions, 
3. Bundled Infrastructure (filesystem, sandbox, browser), 
4. Orchestration Logic (subagent spawning, handoffs, model routing)
5. Hooks/Middleware for deterministic execution (compaction, continuation, lint checks).
---

### Why Harnesses Exist (Problems They Solve)

Out of the box, models cannot: maintain durable state across interactions, execute code, access real-time knowledge, or set up environments and install packages to complete work. These are all harness-level features.

---

### Key Harness Components (and Why Each Exists)

**1. Filesystem** Gives the agent a workspace to read/write data, offload content that doesn't fit in context, and persist work across sessions. Also enables multi-agent collaboration via shared files.

**2. Bash + Code Execution** Instead of forcing users to build tools for every possible action, giving agents a general-purpose tool like bash lets the model design its own tools on the fly via code, rather than being constrained to a fixed set of pre-configured tools. [langchain](https://blog.langchain.com/the-anatomy-of-an-agent-harness/)

**3. Sandboxes** Safe, isolated environments to run agent-generated code. Prevents agents from doing dangerous things locally and allows scale (spin up/down on demand).

**4. Memory & Search** Models have no additional knowledge beyond their weights and what's in their current context. Harnesses support memory file standards like AGENTS.md which get injected into context on agent start — a form of continual learning. Web Search and MCP tools help agents access information beyond the knowledge cutoff. [langchain](https://blog.langchain.com/the-anatomy-of-an-agent-harness/)

**5. Battling Context Rot** Context rot describes how models become worse at reasoning as their context window fills up. Harnesses today are largely delivery mechanisms for good context engineering. [langchain](https://blog.langchain.com/the-anatomy-of-an-agent-harness/) Solutions include:

- **Compaction**: Summarize/offload old context when the window fills up
- **Tool call offloading**: Trim large tool outputs, store full output to filesystem
- **Skills**: Load only relevant tools progressively, not everything at once

**6. Long-Horizon Execution** For complex, multi-session tasks:

- Filesystem + Git tracks progress across sessions
- **[[Ralph Loops]]**: A harness pattern that intercepts the model trying to stop and reinjects the original goal in a fresh context
- Planning + self-verification to stay on track (run tests, loop back on failure)

---

### The Future of Harnesses

#### The Coupling of Model Training and Harness Design

- Claude Code and Codex are post-trained with models and harnesses in the loop. This helps models improve at actions that the harness designers think they should be natively good at like filesystem operations, bash execution, planning, or parallelizing work with subagents.
- This creates a feedback loop. Useful primitives are discovered, added to the harness, and then used when training the next generation of models. As this cycle repeats, models become more capable within the harness they were trained in.
- But this co-evolution has interesting side effects for generalization. It shows up in ways like how changing tool logic leads to worse model performance. A truly intelligent model should have little trouble switching between patch methods, but training with a harness in the loop creates this overfitting.

![](https://storage.ghost.io/c/97/88/97889716-a759-46f4-b63f-4f5c46a13333/content/images/2026/03/image-2.png)

### Where Harness Engineering is Going

- As models get more capable, some of what lives in the harness today will get absorbed into the model. Models will get better at planning, self-verification, and long horizon coherence natively, thus requiring less context injection.
- That suggests harnesses should matter less over time. But just as prompt engineering continues to be valuable today, it’s likely that harness engineering will continue to be useful for building good agents.
- It’s true that harnesses today patch over model deficiencies, but they also engineer systems around model intelligence to make them more effective. A well-configured environment, the right tools, durable state, and verification loops make any model more efficient regardless of its base intelligence.
- Harness building library [deepagents](https://docs.langchain.com/oss/python/deepagents/overview?ref=blog.langchain.com) at LangChain. 



**TL;DR**: The model thinks. The harness acts, remembers, verifies, and keeps the model on track.



