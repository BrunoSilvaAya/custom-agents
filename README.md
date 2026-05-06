# Custom Agents

## Installing a Custom Agent in VS Code

Custom agents are defined as `.agent.md` files placed in the VS Code prompts folder. Once placed there, they appear in the `@` autocomplete list in Copilot Chat.

### Setup

1. **Locate your prompts folder:**
   - **Windows:** `%APPDATA%\Code\User\prompts\`
   - **macOS:** `~/Library/Application Support/Code/User/prompts/`
   - **Linux:** `~/.config/Code/User/prompts/`

2. **Place the `.agent.md` file** in that folder. No restart required — the agent is available immediately.

3. **Invoke the agent** by typing `@AgentName` in the Copilot Chat input, followed by your prompt. For example:
   ```
   @IntegrationAgent INT-3257
   ```

4. **Agent file structure:** Each `.agent.md` file has YAML frontmatter (description, tools, argument-hint) followed by markdown instructions that define the agent's behavior, domain knowledge, and workflow.

### Prerequisites

Some agents require external tools to be installed and configured:

- **Atlassian MCP Server** — Required by agents that fetch Jira tickets (e.g., Integration Agent). Install the [Atlassian MCP extension](https://marketplace.visualstudio.com/items?itemName=Atlassian.atlassian-mcp-for-vscode) and configure it with your Atlassian credentials so the agent can read ticket details, descriptions, and attachments.

---

## Available Agents

### Integration Agent

**File:** `IntegrationAgent.agent.md`
**Invoke:** `@IntegrationAgent`

#### What It Does

Automates building Integration V2 client data pipelines for the Aya Healthcare LotusOne platform. Given a Jira ticket key or a description of the integration, it:

- Classifies the ticket type (new pipeline, mapping update, bug fix, or config change)
- Runs a guided spec wizard to collect requirements
- Researches existing integrations in the codebase for reference patterns
- Generates all deliverables: config JSONs (test + prod), Azure Function triggers, IntegrationTypes constants, and local.settings entries
- Validates the build and summarizes what was created

It handles both **import** pipelines (SFTP → LotusOne) and **export** pipelines (LotusOne → SFTP), including mapping files, SQL queries, and signature files.

#### How to Use It

**Starting from a Jira ticket:**
```
@IntegrationAgent INT-3257  (attach screenshots of the Integration specification)
```
The agent fetches the ticket, extracts requirements, and walks you through the wizard.

**Starting from a description:**
```
@IntegrationAgent Build a Cottage Health EmployeeID import integration
```

**Attaching a spec document:**
The agent works best when you provide the spec. However, it **cannot reliably read `.docx` files** — the content comes through garbled or incomplete. Instead, **take screenshots of the spec document and attach those**. The agent excels at OCR'ing screenshots and extracting the structured table data from the spec images.

**Attaching a sample data file:**
Drag and drop a sample/test file (CSV, TXT) into the chat alongside your prompt. The agent validates the sample against the spec and flags any discrepancies (the spec is always treated as authoritative).

#### Example Prompts

| Prompt | What Happens |
|--------|-------------|
| `@IntegrationAgent INT-3257` | Fetches Jira ticket, runs wizard, builds pipeline |
| `@IntegrationAgent Build UCHealth import.employeeids` | Starts from a description, asks for spec details |
| `@IntegrationAgent Update Adventist export.workers mappings — add facility 99851` | Detects mapping update, skips wizard, applies targeted change |
