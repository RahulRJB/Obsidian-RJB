
# MCP


DATE:  18-11-25


Tags:  [[Notes/Agentic AI|Agentic AI]]

# References:




# Content:

Before MCP, if you wanted your AI Agent to talk to your company's SQL database, Slack, and Google Drive, you had to write specific "glue code" (or a custom [[LangChain]] Tool) for each one.

- If you switched models (e.g., from OpenAI to Claude), you might have to refactor how those tools were called.
- If you wanted to use those same tools in a different agent framework, you had to rewrite them.

### The Solution: "USB-C for AI"

MCP provides a universal standard for how AI models connect to data and tools.

- **The Server (The Tool):** You build an "MCP Server" once for your data source (e.g., a wrapper around your internal API).
- **The Client (The Agent):** Any MCP-compliant Agent (like the Claude Desktop app or a LangChain Agent) can instantly "see," understand, and use that tool without you writing extra code.

### Why it matters for you (The Data Scientist View)

Think of MCP like **ODBC/JDBC** for databases.

- **Without ODBC:** You’d need a specific, custom driver for every single database type you wanted to access.
- **With ODBC:** You have a standard interface. If a database is ODBC-compliant, your analysis tool can read it.

**MCP does the exact same thing, but for AI Tools.**



### The Reference Implementation (Python)

Think of an MCP Server as a **FastAPI app** where the "endpoints" are designed for an LLM, not a web browser.

Here is a conceptual implementation for a Jira MCP server:

Python

```
from mcp.server.fastmcp import FastMCP
from jira import JIRA  # Standard Python Jira library


# 1. Initialize the Server

mcp = FastMCP("My Jira Agent")


# 2. Connect to your Data Source (Standard Python)

jira = JIRA(
    server="https://your-domain.atlassian.net",
    basic_auth=("email@example.com", "YOUR_API_TOKEN")
)


# 3. Define a "Resource" (Read-Only Data)
# The LLM can "read" this like a file.

@mcp.resource("jira://{project_key}/issues")
def get_project_issues(project_key: str) -> str:
    """Returns a list of all open issues in a project."""
    issues = jira.search_issues(f"project={project_key} AND status=Open")
    return "\n".join([f"{i.key}: {i.fields.summary}" for i in issues])


# 4. Define a "Tool" (Action)
# The LLM can "call" this function to change the state.

@mcp.tool()
def create_ticket(summary: str, description: str, priority: str = "Medium") -> str:
    """Creates a new Jira ticket. Use this when the user asks to report a bug."""
    new_issue = jira.create_issue(fields={
        'project': {'key': 'DS_TEAM'},
        'summary': summary,
        'description': description,
        'issuetype': {'name': 'Task'},
        'priority': {'name': priority}
    })
    return f"Ticket created successfully! Key: {new_issue.key}"


# 5. Run the server
if __name__ == "__main__":
    mcp.run()
```

### Key Concepts in this Code

- **Resources (`@mcp.resource`):** Notice `get_project_issues`. In MCP, we often expose data as "resources" (like virtual files). An Agent can "read" `jira://PROJ-1/issues` to get context _before_ it tries to solve a problem.
    
- **Tools (`@mcp.tool`):** Notice `create_ticket`. The Docstring (`"""..."""`) is crucial. The LLM uses that text to understand _when_ and _how_ to call this Python function.
    
- **Type Hints (`str`, `int`):** MCP uses these to validate the LLM's inputs automatically. If the LLM tries to send a number for the `summary`, the server rejects it.