
# Tree-of-Thoughts


DATE:  18-11-25


Tags:  [[Notes/Agentic AI|Agentic AI]]

# References:




# Content:


**The "Search" Approach**

Standard Chain-of-Thought is a "greedy" process—it picks the most likely next word and commits to it forever. If the model makes a mistake in Step 1, the whole answer fails. ToT treats reasoning as a **search problem** (like a chess computer looking at future moves).

- **Core Concept:** The model explicitly explores multiple "reasoning paths" (branches), self-evaluates them, and backtracks if a path looks like a dead end.
- **The Process:**
    1. **Decomposition:** The problem is broken into steps (e.g., writing a paragraph, solving a math equation).
    2. **Generation:** At each step, the model generates _multiple_ valid next steps (Branches A, B, and C).
    3. **Evaluation:** The model (or a separate "voter" prompt) scores each branch (e.g., "Branch A is promising," "Branch B is impossible").
    4. **Search Algorithm:** It uses standard algorithms like BFS (Breadth-First Search) or DFS (Depth-First Search) to navigate these branches.


**Example: The "Game of 24" (Using numbers 4, 9, 10, 13 to get 24)**

- **Standard CoT:** "4 + 9 = 13. 13 + 10 = 23. 23 + 1... oh wait I have to use 13. I can't solve it." (Fails because it committed to 4+9 early).

- **Tree-of-Thoughts Process:**
    
    - _Step 1 Candidates:_
        - (Branch A) 4 + 9 = 13
        - (Branch B) 13 - 9 = 4
        - (Branch C) 13 * 4 = 52
    - _Evaluation:_ Model looks at the remaining numbers. Branch B looks promising because we have another 10 and 4 left. Branch C is too high.
    - 
    - _Step 2 (Continuing from Branch B):_
        - Left with {4, 10, 4}.
        - (Branch B1) 10 - 4 = 6 (Remaining: 4). 6 * 4 = 24. **Solved.**



### Summary of Differences between ReAct and ToT

| **Feature**   | **ReAct**                                                                                                                    | **Tree-of-Thoughts (ToT)**                                                                                                                                |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Best For**  | **Knowledge retrieval & Action.** Tasks requiring current data, precise calculations (calculator), or interacting with APIs. | **Complex Logic & Strategy.** Tasks like creative writing, strategic planning, or difficult puzzles (Math, Coding) where "one-shot" thinking often fails. |
| **Mechanism** | **Cyclic.** Think $\to$ Act $\to$ Observe loop.                                                                              | **Tree Search.** Generate multiple options $\to$ Score $\to$ Select best path.                                                                            |
| **Weakness**  | Can get stuck in loops (repeating the same search). Slow due to external calls.                                              | Extremely computationally expensive (requires generating many branches for every single step).                                                            |

### Note on "Reasoning Models"

Modern reasoning models (like OpenAI's o1 or DeepSeek-R1) effectively **internalize** these patterns.

- They act like **ToT** by generating many internal "thought tokens" to explore different paths and discarding the bad ones before showing you the answer.
- They act like **ReAct** by having built-in tools (like Python interpreters) that they can call internally to verify their own code or math before finalizing the output.



