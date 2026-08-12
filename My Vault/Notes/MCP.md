
# MCP


DATE:  18-11-25


Tags:  [[Notes/Agentic AI|Agentic AI]]

# References:

- https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro
- https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture
- https://modelcontextprotocol.io/docs/2026-07-28/learn/server-concepts
- https://modelcontextprotocol.io/docs/2026-07-28/learn/client-concepts


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

---

## Architecture (official spec, 2026-07-28)

### Participants: Host / Client / Server

MCP is client-server, with three roles:

- **MCP Host** — the AI application itself (e.g. Claude Desktop, Claude Code, VS Code). It coordinates one or more MCP clients.
- **MCP Client** — lives *inside* the host. One client is instantiated per server connection, and each client keeps a **dedicated 1:1 connection** to its server.
- **MCP Server** — the program that exposes tools/resources/prompts. "Server" refers to the program, not where it physically runs.

So a host talking to 3 servers has 3 client objects, each with its own private connection — clients don't share or fan out across servers.

- **Local servers** (e.g. filesystem) typically use **stdio** transport and serve a single client (one process per connection).
- **Remote servers** (e.g. Sentry) typically use **Streamable HTTP** and can serve many clients concurrently.

### Two layers

1. **Data layer** — the JSON-RPC 2.0 based protocol: message structure, discovery, primitives, notifications. This is the layer most developers actually think about, since SDKs abstract the transport.
2. **Transport layer** — how bytes actually move: connection setup, framing, auth. Wraps the data layer.

**Transports:**
- **Stdio** — stdin/stdout between local processes, no network overhead. Used for local servers.
- **Streamable HTTP** — HTTP POST client→server, optional SSE for server→client streaming. Used for remote servers; supports bearer tokens/API keys/OAuth.

The same JSON-RPC message format is used regardless of which transport carries it.

### Statelessness + discovery

MCP requests are **stateless** — every request carries its own protocol version and capabilities in a `_meta` field, so the server never needs to infer context from earlier requests. A client *may* call `server/discover` first to learn the server's supported protocol versions, capabilities (tools/resources/prompts support, notification support), and identity — but this is optional since every request is self-describing anyway. Discovery responses are cacheable (`ttlMs`, `cacheScope`).

### Primitives

**Exposed by servers** (what a server can offer a client):
- **Tools** — executable functions the AI can invoke to take action (API calls, DB writes, file ops). Discovered via `tools/list`, invoked via `tools/call`. Each tool has a `name`, `description`, and JSON Schema `inputSchema` — the description is what the LLM reads to decide when/how to call it.
- **Resources** — read-only contextual data (file contents, DB records) the AI can pull in, like handing it a file.
- **Prompts** — reusable interaction templates (system prompts, few-shot examples).

**Exposed by clients** (what a server can ask of the client):
- **Elicitation** — lets a server ask the *user* for more input or confirmation mid-task, via `elicitation/create`.
- ~~Sampling~~ / ~~Logging~~ — **deprecated** as of `2026-07-28`. Sampling (server asking the client's LLM for a completion, to stay model-agnostic) should now integrate directly with LLM provider APIs; logging should go to stderr/OpenTelemetry instead.

### Notifications (real-time updates)

Opt-in, subscription-based: a client opens a long-lived stream via `subscriptions/listen` naming the event types it wants (e.g. `toolsListChanged`). The server acks, then pushes JSON-RPC *notifications* (no `id`, no response expected) when state changes — e.g. `notifications/tools/list_changed` when available tools change. This avoids polling and keeps the host's tool registry in sync with the model mid-conversation. Delivery is best-effort — clients shouldn't rely on it exclusively over unreliable transports/reconnects.

### End-to-end flow

`server/discover` (optional, learn capabilities) → `tools/list` (see what's available) → `tools/call` (invoke with validated args, get back a `content` array — text/image/resource) → optionally `subscriptions/listen` to stay updated if the tool set changes later.

### Mental model tying it together

MCP only standardizes the **protocol for exchanging context** (tools/resources/prompts) between host and server — it deliberately says nothing about how the host's LLM decides to use that context. That separation is why the "USB-C" analogy holds: the plug shape is standardized, what's on the other end (a Jira server, a filesystem, a DB) isn't the protocol's concern.

---

## Servers — the three building blocks, and who controls each

The server-concepts doc frames tools/resources/prompts by **who decides to use them**, which is a sharper lens than just "what they are":

| Feature | What it is | Who controls it | Protocol ops |
|---|---|---|---|
| **Tools** | Functions the LLM can actively call to take action (write DB, call API, modify files) | **Model** — the LLM decides when to invoke | `tools/list`, `tools/call` |
| **Resources** | Passive, read-only context (file contents, DB schema, docs) | **Application** — the host decides what to fetch/inject | `resources/list`, `resources/templates/list`, `resources/read`, `subscriptions/listen` |
| **Prompts** | Pre-built instruction templates for using specific tools/resources | **User** — requires explicit invocation, e.g. a slash command | `prompts/list`, `prompts/get` |

That "who controls it" column is the practical takeaway: tools are autonomous (model-triggered, hence need consent/approval UI), resources are silently pulled in by the app itself, prompts are never triggered without a user explicitly picking them.

**Resources come in two discovery flavors:**
- **Direct resources** — fixed URI, e.g. `calendar://events/2024`.
- **Resource templates** — parameterized URI, e.g. `travel://activities/{city}/{category}`. These support **parameter completion** (typing "Bar" suggests "Barcelona") so users can discover valid values without knowing the exact format.

**Tools need user oversight** even though the model triggers them — MCP expects the host to provide guardrails: showing available tools in the UI, per-call approval dialogs, pre-approved "safe" operations, and activity logs of what ran.

**Prompts** are the most structured of the three — they declare typed `arguments` (like a tool's `inputSchema`) so a "plan-vacation" prompt can force structured input (destination, duration, budget) instead of free text, and are commonly surfaced as slash commands or a command palette.

### Multi-server composition

The doc's running example (a travel planner) shows the real point of MCP: three independent servers (Travel, Weather, Calendar/Email) combine through one host without knowing about each other. A single `plan-vacation` prompt invocation causes the AI to: read **resources** from the Calendar and Travel servers for context (availability, past trips) → call **tools** across servers (`searchFlights`, `checkWeather`, `bookHotel`, `createCalendarEvent`, `sendEmail`), asking for approval where needed. Each server only implements its own domain; the composition happens entirely at the host/model level.

---

## Clients — what a client can offer back to a server

Where servers expose tools/resources/prompts *to* the client, the client-concepts doc covers the reverse direction: features a **client** exposes *to* servers, so server authors can build richer, more interactive flows instead of only ever returning static results.

| Feature | Purpose | Status |
|---|---|---|
| **Elicitation** | Server asks the user for info mid-operation (e.g. seat preference) | Live |
| **Roots** | Client tells server which filesystem directories are in scope | **Deprecated** in `2026-07-28` — use tool params/resource URIs/server config instead |
| **Sampling** | Server asks the client's LLM to generate a completion | **Deprecated** in `2026-07-28` — servers should call an LLM API directly instead |

### Elicitation (the one still live)

Lets a server pause mid-request and ask the user for more input rather than failing or requiring everything up front. Two modes:
- **Form mode** — server sends a JSON Schema, client renders a form, validates the response against the schema.
- **URL mode** — server hands the client a URL to open (out-of-band); used for anything sensitive like credentials or OAuth, since that data then never passes through the client or LLM context at all. **Rule of thumb: passwords/API keys/payment info must go through URL mode, never form mode.**

Mechanically it rides the same **Multi Round-Trip Requests (MRTR)** pattern: the server's `tools/call` response comes back as an `InputRequiredResult` carrying an `elicitation/create` request → client shows UI, collects input → client retries the *original* request with `inputResponses` attached → server continues and returns the final result.

### Roots (deprecated)

Client-declared `file://` boundaries telling a server "operate within these directories." Important nuance: roots are **advisory, not a security boundary** — the spec says servers "SHOULD respect" them, not "MUST enforce," because the client can't actually control code running in the server process. Real enforcement has to happen at the OS level (permissions/sandboxing). Superseded by passing paths via tool params / resource URIs / server config.

### Sampling (deprecated)

Let a server request an LLM completion *through the client* — useful so servers don't need their own model API key/integration, and so the client retains control over cost/permissions. Followed the same MRTR pattern as elicitation, but with a `sampling/createMessage` request, `modelPreferences` (cost/speed/intelligence priority hints), and **two human-in-the-loop checkpoints** — approve the outgoing prompt, then approve the returned completion — before the client retries the original call. Now superseded by servers integrating directly with a provider's API, which is simpler and avoids burning the *client's* context/budget on work that's really the server's concern.

### Why this split matters

Servers own tools/resources/prompts (context → model); clients own elicitation/roots/sampling (model/user → server). The deprecations in `2026-07-28` (roots, sampling, and logging on the server side) all trend the same direction: **collapse responsibilities that used to round-trip through the client back to where they logically belong** — filesystem scoping into tool/resource params, model calls into the server's own provider integration. Elicitation survives because it's genuinely something only the *client* can do (it owns the user-facing UI).