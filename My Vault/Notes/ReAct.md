
# ReAct (Reasoning and Acting)


DATE:  18-11-25


Tags:  [[Notes/Agentic AI|Agentic AI]]

# References:



# Content:


**The "Agent" Approach**

Standard Chain-of-Thought allows a model to _talk_ to itself. ReAct allows a model to _talk_ to the world. It addresses a major limitation of pure reasoning: **hallucination** and **lack of external knowledge**.

- **Core Concept:** Instead of just thinking through a problem linearly, the model enters a loop where it can "Action" (use a tool) and "Observe" (get data back).
- **The Loop:** The prompt enforces a strict format: `Thought` → `Action` → `Observation`.
    - **Thought:** The model reasons about what it needs to do.
    - **Action:** The model emits a specific command (e.g., `SEARCH["Age of current US President"]`). A customized script (the "controller") stops the model here, executes the code/search, and fetches the result.
    - **Observation:** The controller feeds the result (e.g., "Joe Biden, 80") back into the model prompt.
    - **Repeat:** The model uses this new observation to formulate the next Thought.


**Example: "Who is the wife of the actor who played Neo in Matrix?"**

- **Standard CoT:** "Neo is played by Keanu Reeves. I think he is married to... [Hallucinates] Alexandra Grant." (Incorrect, they are dating but not married).
- **ReAct Process:**
    - _Thought 1:_ I need to find out who played Neo.
    - _Action 1:_ `Search["Neo actor Matrix"]`
    - _Observation 1:_ Keanu Reeves.
    - _Thought 2:_ Now I need to find Keanu Reeves' spouse.
    - _Action 2:_ `Search["Keanu Reeves spouse"]`
    - _Observation 2:_ Keanu Reeves has never been married.
    - _Final Answer:_ Keanu Reeves is the actor, and he is not married.


### Summary of Differences between ReAct and ToT

| **Feature**   | **ReAct**                                                                                                                    | **Tree-of-Thoughts (ToT)**                                                                                                                                |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Best For**  | **Knowledge retrieval & Action.** Tasks requiring current data, precise calculations (calculator), or interacting with APIs. | **Complex Logic & Strategy.** Tasks like creative writing, strategic planning, or difficult puzzles (Math, Coding) where "one-shot" thinking often fails. |
| **Mechanism** | **Cyclic.** Think $\to$ Act $\to$ Observe loop.                                                                              | **Tree Search.** Generate multiple options $\to$ Score $\to$ Select best path.                                                                            |
| **Weakness**  | Can get stuck in loops (repeating the same search). Slow due to external calls.                                              | Extremely computationally expensive (requires generating many branches for every single step).                                                            |

### Note on "Reasoning Models"

Modern reasoning models (like OpenAI's o1 or DeepSeek-R1) effectively **internalize** these patterns.

- They act like **ToT** by generating many internal "thought tokens" to explore di2025*Sepsepfferent paths and discarding the bad ones before showing you the answer.
- They act like **ReAct** by having built-in tools (like Python interpreters) that they can call internally to verify their own code or math before finalizing the output.


