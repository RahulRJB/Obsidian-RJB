
# Agentic RAG


DATE:  17-01-25


Tags: [[Notes/RAG|RAG]]

# References:


# Content:

Agentic RAG represents an evolution of traditional RAG (Retrieval-Augmented Generation) systems. While traditional RAG simply retrieves relevant documents and feeds them directly into a language model, Agentic RAG treats the retrieval process as an interactive, multi-step reasoning task.

Think of traditional RAG as a librarian who simply hands you all potentially relevant books when you ask a question. In contrast, Agentic RAG is more like a skilled research assistant who actively thinks about what information they need, searches for it strategically, evaluates what they find, and may perform multiple rounds of research to build a complete answer.

Here's how Agentic RAG works step by step:

First, when given a query, the system doesn't immediately search the knowledge base. Instead, it plans out what information it needs to answer the question completely. It might break down complex queries into smaller sub-questions or identify key concepts it needs to research.

Next, it actively searches for information using this plan. Rather than making a single search, it might perform multiple targeted searches, each informed by what it learned in previous steps. For example, if researching a company's performance, it might first search for recent financial data, then use those findings to guide searches for relevant market conditions or competitor information.

The system then evaluates the retrieved information critically. It assesses the relevance and reliability of sources, identifies gaps in the gathered information, and determines if additional searches are needed. This is similar to how a human researcher might cross-reference sources or seek out additional context when initial findings are incomplete.

Finally, the system synthesizes the information into a coherent response. Rather than simply summarizing the retrieved content, it combines information from multiple sources, resolves contradictions, and generates new insights based on the collected evidence.

What makes this approach particularly powerful is its ability to handle complex queries that require multiple steps of reasoning. For instance, if asked "How has remote work affected commercial real estate in major cities since the pandemic?", an Agentic RAG system might:

1. Research remote work adoption trends
2. Use those findings to guide searches about office vacancy rates
3. Look into commercial real estate market data
4. Search for specific city-by-city comparisons
5. Finally, synthesize all this information into a comprehensive analysis

The "agentic" aspect comes from the system's ability to make autonomous decisions about what information to seek and how to use it, much like a human agent would. This represents a significant advancement over traditional RAG systems, which are more mechanical in their retrieval and generation process.

An important limitation to note is that the effectiveness of Agentic RAG heavily depends on the quality of its underlying components: the language model's reasoning capabilities, the sophistication of its search strategies, and the comprehensiveness of its knowledge base. The system also needs carefully designed prompts or instructions to guide its autonomous decision-making process effectively.



Essential components:


```
from langchain import LLMChain, PromptTemplate, OpenAI 
from langchain.agents import Tool, AgentExecutor, create_react_agent 
from langchain.tools import DuckDuckGoSearchRun 
from langchain.memory import ConversationBufferMemory 
from langchain.embeddings import OpenAIEmbeddings 
from langchain.vectorstores import FAISS
```

1. The Knowledge Base Setup

```
def create_knowledge_base(documents):     
	embeddings = OpenAIEmbeddings()
	vectorstore = FAISS.from_documents(documents, embeddings)
	retriever = vectorstore.as_retriever(        search_type="similarity",        search_kwargs={"k": 5}    )    
	
	return retriever
```


2. The Planning Component

`def create_planner():     planner_template = """    Goal: {query}         Create a step-by-step plan to find the necessary information.    Consider what specific information is needed and in what order.         Plan:"""         planner_prompt = PromptTemplate(        input_variables=["query"],        template=planner_template    )         planner = LLMChain(        llm=OpenAI(temperature=0),        prompt=planner_prompt    )    return planner`


3. The Research Tools

`def create_research_tools(retriever):     tools = [        Tool(            name="Search Knowledge Base",            func=retriever.get_relevant_documents,            description="Search internal documents for information"        ),        Tool(            name="Web Search",            func=DuckDuckGoSearchRun(),            description="Search the web for current information"        )    ]    return tools`


4. The Reasoning Agent

`def create_agent(tools):     agent_template = """    You are a research assistant tasked with finding accurate information.    Use the following tools to research the query: {tools}         Current task: {input}    Relevant context: {context}         Think step by step:    1) What specific information do I need?    2) Which tool is best for finding this information?    3) How can I verify this information?         {agent_scratchpad}    """         prompt = PromptTemplate(        input_variables=["tools", "input", "context", "agent_scratchpad"],        template=agent_template    )         agent = create_react_agent(        llm=OpenAI(temperature=0.7),        tools=tools,        prompt=prompt    )         return AgentExecutor.from_agent_and_tools(        agent=agent,        tools=tools,        memory=ConversationBufferMemory(),        verbose=True    )`


5. Putting It All Together

`class AgenticRAG:     def __init__(self, documents):        self.retriever = create_knowledge_base(documents)        self.planner = create_planner()        self.tools = create_research_tools(self.retriever)        self.agent = create_agent(self.tools)             def answer_query(self, query):        # Generate research plan        plan = self.planner.run(query)                 # Execute research steps        research_results = self.agent.run(            input=query,            context=f"Research plan: {plan}"        )                 # Synthesize final answer        synthesis_prompt = f"""        Query: {query}        Research findings: {research_results}                 Synthesize these findings into a comprehensive answer.        """                 final_answer = OpenAI(temperature=0.3).generate(synthesis_prompt)        return final_answer`

To use this system effectively, there are several important considerations:

1. Prompt Engineering: The prompts provided above are simplified examples. In practice, you'll need to craft more detailed prompts that guide the agent's behavior, including:
    - How to evaluate source reliability
    - When to dig deeper vs. move on
    - How to handle contradictory information
    - How to identify information gaps
2. Error Handling: You'll want to add robust error handling for cases where:
    - The agent can't find relevant information
    - External APIs fail
    - The plan needs to be revised mid-execution
3. Performance Optimization: Consider implementing:
    - Caching for frequently accessed information
    - Parallel processing for multiple research steps
    - Rate limiting for external API calls
4. Monitoring and Logging: