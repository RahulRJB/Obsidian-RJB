
# Reasoning Models


DATE:  18-11-25


Tags: [[Notes/LLMs|LLMs]]

# References:




# Content:

## 🧠 Do Reasoning Models Use Chain-of-Thought?

**Yes, reasoning models _do_ use a Chain-of-Thought process, but it is often an internal and intrinsic part of their function, not just a prompt.**

1. **[[CoT]] as a Prompting Trick:** Initially, [[CoT]] was a simple prompt instruction to make a standard LLM break down its answer.

2. **Reasoning Models Internalize CoT:** Reasoning models are trained to make this step-by-step processing **intrinsic** to their function. They generate an **internal chain of thought** (or reasoning traces) before producing the final response. They use these "reasoning tokens" as a scratchpad or working memory to break down, analyze, and even self-correct their logic.

3. **Advanced Reasoning Frameworks:** Modern reasoning models go beyond simple linear CoT and can incorporate more sophisticated frameworks like:
    1. **[[Tree-of-Thoughts]] (ToT):** Exploring multiple reasoning paths simultaneously and evaluating them.
    2. **[[ReAct]] (Reasoning and Acting):** Combining internal reasoning steps with calling external tools (like search or a calculator) to verify information or perform calculations.



### How They Work Exactly

Reasoning models work by using **additional computational steps** at inference time, guided by specialized training:

1. **Reinforcement Learning and Process Supervision:** They are often fine-tuned using Reinforcement Learning to reward the quality and correctness of the _intermediate reasoning steps_ (known as **process supervision**), not just the final answer (**outcome supervision**). This trains the model to be a better "thinker."
    
2. **Internal Thought Generation:** When a user poses a complex problem, the reasoning model allocates **extra tokens** (the reasoning tokens) to internally generate a detailed, step-by-step plan or calculation (the chain of thought). This is like a senior colleague working out the full solution on a scratchpad.
    
3. **Self-Correction and Tool Use:** This internal reasoning process can include logical checks, iterative refinement, and decisions on when to invoke external tools (like code interpreters or web search) to perform an action or verify a step.
    
4. **Final Output Synthesis:** After the internal reasoning is complete and a confident answer is reached, the model synthesizes this into the final, concise, and accurate response presented to the user, often discarding the intermediate "thought" tokens.


**The key insight: reasoning models use CoT, but as a trained capability with much more compute-at-inference-time, rather than just a prompting trick.**
