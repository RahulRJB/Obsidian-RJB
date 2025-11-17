
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



