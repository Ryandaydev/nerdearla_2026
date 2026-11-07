# NERDEARLA 2026 Demo — New Codespace Setup Guide

Use this checklist when creating a fresh GitHub Codespace from the repository.

> **Important:** The public repository does not include your private `DATABASE_URL`.  
> Your own Codespace can receive it from your GitHub Codespaces secrets, but other users who clone or create a Codespace from the public repo must configure their own database connection.

---

## 1. Create the Codespace

Create a new Codespace from the repository.

If you are rehearsing the live demo and do not want to work directly on `main`, switch to your demo branch:

```bash
git switch <demo-branch-name>
```

Confirm the branch:

```bash
git branch --show-current
```

---

## 2. Presentation Setup in VS Code

Before coding, make the editor presentation-friendly.

### Shorten the path display

Configure VS Code so file paths show only one folder level.

### Set a readable zoom level

Increase the VS Code zoom until the editor, terminal, and Copilot panels are easy to read on the presentation screen.

### Optional: hide deprecated-symbol strikethroughs

If Pylance shows distracting strikethroughs on symbols such as `@asynccontextmanager`, add this to your VS Code workspace settings:

```json
"python.analysis.diagnosticSeverityOverrides": {
    "reportDeprecated": "none"
}
```

Reload the VS Code window if needed:

```text
Ctrl+Shift+P
Developer: Reload Window
```

---

## 3. Verify the Database Secret

Check that the Codespace received your Supabase connection string:

```bash
echo $DATABASE_URL
```

It should point to Supabase, **not**:

```text
localhost:5432
```

For this project, SQLAlchemy uses `asyncpg`, so the URL should use a prefix like:

```text
postgresql+asyncpg://
```

Do not commit the real connection string to the public repository.

If `DATABASE_URL` is missing, configure it as a GitHub Codespaces secret before continuing.

---

## 4. Set Up the FastAPI Project

Move into the API folder:

```bash
cd flights-api
```

Install/synchronize dependencies:

```bash
uv sync
```

Start the API using the project’s normal FastAPI command.

For example:

```bash
uv run fastapi dev main.py
```

Verify the API is running.

Open the FastAPI docs:

```text
/docs
```

Test the existing flights endpoint and confirm it returns data from Supabase.

---

## 5. If Port 8000 Is Already in Use

Check which process owns the port:

```bash
lsof -i :8000
```

Kill the old FastAPI/Python processes:

```bash
kill <PID1> <PID2>
```

If they do not stop:

```bash
kill -9 <PID1> <PID2>
```

Or kill everything currently listening on port 8000:

```bash
kill -9 $(lsof -t -i:8000)
```

Confirm the port is free:

```bash
lsof -i :8000
```

Then start the API again.

---

## 6. Verify the Existing SDK

Open the simple Jupyter notebook used for the demo.

Suggested notebook title:

```markdown
### **Demo muy simple del SDK | Very simple SDK demo**
```

Create the async client:

```python
from air_travel import AsyncAirTravelClient

client = AsyncAirTravelClient()
```

Call an existing SDK method, for example the flights method:

```python
flights = await client.flights(limit=5)
flights
```

This proves the SDK can call the running API successfully.

---

## 7. Live Coding Part 1 — Add Airlines to the API

Add the new airlines endpoint to the FastAPI project.

The purpose is to return airline names and their codes so the system can resolve a common name such as:

```text
United Airlines
```

to a code such as:

```text
UA
```

Save the file and test the new endpoint in:

```text
/docs
```

Confirm that it returns the airline names and codes.

---

## 8. Live Coding Part 2 — Add Airlines to the SDK

Add the new async SDK method:

```python
airlines()
```

The method should call the new API endpoint.

After changing the SDK, restart the Jupyter kernel so the notebook loads the updated Python class.

In VS Code:

```text
Restart Kernel
```

Then rerun the import/client cells.

Test the new SDK method:

```python
airlines = await client.airlines()
airlines
```

At this point, the new functionality exists at both levels:

```text
API → SDK
```

---

## 9. Verify the MCP Workspace Configuration

The MCP server should be configured in the **remote workspace**, not only in your local VS Code user configuration.

Show this file during the presentation:

```text
.vscode/mcp.json
```

The configuration should tell VS Code how to start the Air Travel MCP server.

Make sure there is only one Air Travel server configuration.

If VS Code shows duplicate MCP servers, check for an old local user configuration:

```text
Ctrl+Shift+P
MCP: Open User Configuration
```

Remove the duplicate local entry so the demo uses the remote Codespace/workspace configuration.

Reload VS Code if needed:

```text
Ctrl+Shift+P
Developer: Reload Window
```

---

## 10. Start the MCP Server

Make sure the FastAPI API is still running first.

Then start the configured Air Travel MCP server in VS Code.

Verify that VS Code discovers the existing MCP tools.

Before making the MCP code change, test the existing flights tool.

For example, ask Copilot for flights using a known airline code such as:

```text
UA
```

A successful result proves this chain is working:

```text
Copilot → MCP server → SDK → FastAPI → Supabase
```

---

## 11. Live Coding Part 3 — Add the Airline Codes MCP Tool

Add the new MCP tool that calls:

```python
get_client().airlines()
```

The tool description should explain that the agent can use it when it knows the common airline name but needs the airline code before calling the flights tool.

Example behavior:

```text
United Airlines
      ↓
get_airline_codes
      ↓
UA
      ↓
get_flights
```

Save the MCP server code.

---

## 12. Reload the MCP Server

Restart or reload the MCP server in VS Code so it discovers the new tool.

Verify that the airline-codes tool now appears in the available MCP tools.

---

## 13. Final Demo Test

Ask Copilot a question using the **airline name**, not the code.

For example:

```text
Find flights for United Airlines on Thanksgiving 2025.
```

The ideal tool sequence is:

```text
1. get_airline_codes
2. Resolve United Airlines → UA
3. get_flights
4. Return the flight results
```

This is the main payoff of the demo: the agent can combine multiple MCP tools to solve the request.

---

## Quick Pre-Demo Checklist

Before presenting, confirm:

- [ ] Correct demo branch is checked out
- [ ] VS Code paths are shortened
- [ ] VS Code zoom is readable
- [ ] `DATABASE_URL` points to Supabase
- [ ] API dependencies are synced with `uv sync`
- [ ] FastAPI is running
- [ ] Existing flights endpoint works
- [ ] Jupyter notebook can call the SDK
- [ ] `.vscode/mcp.json` exists
- [ ] No duplicate local MCP server configuration
- [ ] Existing MCP flights tool works
- [ ] API is still running before the MCP test
- [ ] Final airline-name-to-flight tool chain works

