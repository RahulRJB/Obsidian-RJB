
# LLM Agents


DATE:  13-12-24


Tags: [[Notes/LLMs|LLMs]] [[Notes/RAG|RAG]]

# References: 
https://youtube.com/watch?v=5CLNoPiMbUc




# Content:

 - **LLM agents are systems that use Large Language Models (LLMs) at its core to perform high level tasks and interact with their environment.** They go beyond simply responding to prompts by integrating with tools and decision-making processes. It achieves this by leveraging additional modules including memory planning and action.
- **Environment** This is everything the agent can interact with – including the user, the tools, data sources, and the internet.
- **Tools & APIs:** LLM agents don't just generate text. They are designed to interact with the "real world" by using tools and APIs. These can be:
    - **Search engines:** To find information on the internet.
    - **Calculators:** To perform arithmetic operations.
    - **Databases:** To access and manage data.
    - **Web browsers:** To navigate and interact with web pages.
    - **Code interpreters:** To execute code and perform complex logic.
    - **Custom tools:** Developed for specific tasks or domains.

- **High level tasks** are complex tasks that need to be broken down into smaller low-level tasks that are easier to understand and execute in a specific environment.![[Attachments/Pasted image 20241213124255.png]]
- **Key Characteristics of LLM Agents:**
	- **Autonomy:** Agents can operate independently to achieve a goal without requiring step-by-step instructions. They decide which actions to take and in what order.
	- **Planning & Reasoning:** Agents can break down complex tasks into smaller steps and plan their execution. They can reason about the best course of action.
	- **Memory:** Agents can store information about their past interactions and use this knowledge to make better decisions in the future (short-term and long-term).
	- **Action:** They can take actions within their environment using the tools available.
	- **Observation:** Agents can observe the results of their actions and use this feedback to adjust their strategy.
	- **Iteration:** Agents can learn from their mistakes and improve their performance over time.
	- **Context Awareness:** They maintain a record of their interactions to understand context and user intent.

- **How They Work (Simplified):**
1. **User Input:** The user provides a goal or request in natural language.
2. **LLM Processing:** The LLM within the agent processes the input and identifies the objective.
3. **Planning:** The agent develops a plan to achieve the objective, deciding what tools to use and what actions to take.
4. **Action:** The agent executes the planned actions by interacting with its environment (e.g., uses a search engine, makes an API call).
5. **Observation:** The agent observes the results of its actions.
6. **Reasoning & Iteration:** The agent analyzes the observation, updates its plan (if necessary), and repeats steps 4-5 until the goal is achieved.
7. **Output:** The agent presents the results or a summary to the user.

- **Examples of LLM Agent Applications:**
	- **Personal Assistants:** Booking appointments, managing schedules, answering questions, setting reminders.
	- **Content Creation:** Generating blog posts, articles, social media content, code, creative writing.
	- **Research & Analysis:** Gathering information, analyzing data, summarizing findings.
	- **Customer Service:** Answering customer queries, resolving issues, providing support.
	- **Autonomous Code Generation:** Translating requirements into code, debugging, suggesting code improvements.
	- **Robotics:** Planning robot actions in dynamic environments.

- **Key Differences Between LLMs and LLM Agents:**
	- **LLM (Basic):** Responds to prompts, generates text, but doesn't have tools or memory.
	- **LLM Agent:** Uses an LLM as its brain and can perform tasks in an environment by using tools, making plans, and taking actions based on feedback and memory.

- ![[Attachments/Pasted image 20241213131524.png]]![[Attachments/Pasted image 20241213131548.png]]![[Attachments/Pasted image 20241213131708.png]]![[Attachments/Pasted image 20241213134431.png]]



