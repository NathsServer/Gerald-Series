# Episode 4 — MCP Servers

> 👈 **New here?** Start with the [main README](../README.md) for a full overview of the Gerald project, what it does, and what you'll be building across all episodes.
>
> **Coming from Episode 3?** Make sure you have the [Episode 3](../Episode%203/README.md) stack running before continuing.

This episode replaces the old SSH-based tool approach with a proper **Model Context Protocol (MCP)** layer. Five MCP servers are added to the Docker stack via Supergateway, and Gerald gets a new unified **Gerald Tools** sub-agent that routes all tool calls through them.

By the end of this episode Gerald will be able to:
- Read and write files on your server via the Filesystem MCP server
- Search the web in real time via the Brave Search MCP server
- Interact with GitHub repositories via the GitHub MCP server
- List and manage Ollama models via the Ollama MCP server
- Query your Postgres database directly via the Postgres MCP server

---

## Files in This Folder

| File | What It Is |
|------|-----------|
| `docker-compose.yml` | Updated Docker stack with five MCP servers added |
| `.env` | Environment variables — add your Brave and GitHub keys here |
| `Gerald Main.json` | Updated main Gerald workflow — routes tool tasks to Gerald Tools |
| `Gerald Tools.json` | New Gerald Tools sub-agent workflow — handles all MCP tool calls |
| `AI Server SSH Tool.json` | SSH tool workflow (carried over for reference) |
| `Gerald Main System Message` | System prompt for the main Gerald agent |
| `Gerald Tools System Message` | System prompt for the Gerald Tools sub-agent |
| `Call Gerald Tools DESC` | Tool description text used in the Gerald Main workflow |
| `Commands in Order` | Quick reference of the terminal commands used in this episode |

---

## Prerequisites

- Docker & Docker Compose installed
- n8n running (see [Episode 1](../Episode1/README.md))
- **Brave Search API key** — [https://api.search.brave.com/app/keys](https://api.search.brave.com/app/keys) (free tier available — setting a small monthly spend limit is recommended)
- **GitHub Personal Access Token** — prefer a [fine-grained PAT](https://github.com/settings/personal-access-tokens/new) scoped to only the repositories Gerald needs to access, with **Contents: Read** and **Metadata: Read** permissions. If you must use a classic token ([https://github.com/settings/tokens](https://github.com/settings/tokens)), grant only the `repo` scope and avoid `admin:*` or `write:*` scopes to minimise blast radius if the token is ever leaked.

---

## Step-by-Step Setup

### 1. Add your API keys to `.env`

Open your `.env` file and add the two new keys:

```env
BRAVE_API_KEY=your_brave_api_key
GITHUB_TOKEN=your_github_pat
```

### 2. Update `docker-compose.yml`

Replace your existing `docker-compose.yml` with the one from this episode's folder. It adds five new MCP server containers to the stack.

### 3. Restart the Docker stack

```bash
sudo docker compose up -d
```

This will pull the new container images and start the MCP servers alongside the existing n8n, Ollama, and Postgres services.

### 4. Verify the MCP servers are running

Once the stack is up, check that each MCP server is responding. SSE connections stay open by design, so `--max-time 3` is used to get the initial event and exit:

```bash
curl --max-time 3 http://localhost:8881/sse  # Filesystem
curl --max-time 3 http://localhost:8882/sse  # Brave Search
curl --max-time 3 http://localhost:8883/sse  # GitHub
curl --max-time 3 http://localhost:8884/sse  # Ollama
curl --max-time 3 http://localhost:8885/sse  # Postgres
```

Each should print something like the following and then exit:

```
event: endpoint
data: /message?sessionId=...
```

If a server doesn't respond (or curl exits immediately with an error), check its logs with `sudo docker compose logs <container-name>` (e.g. `mcp-brave`).

### 5. Import the updated workflows into n8n

Inside n8n, import and activate both new workflows:
1. `Gerald Main.json`
2. `Gerald Tools.json`

Deactivate the old Episode 3 versions of these workflows to avoid conflicts.

### 6. Add the MCP Tool nodes in n8n

Follow the video to wire up the five MCP SSE connections inside the **Gerald Tools** workflow — one for each MCP server (Filesystem, Brave Search, GitHub, Ollama, Postgres).

---

## Troubleshooting

- **An MCP server won't start** — check `sudo docker compose logs <container>` (e.g. `mcp-brave`). Missing environment variables are the most common cause.
- **Brave Search returns errors** — confirm `BRAVE_API_KEY` is set correctly in `.env` and that you haven't exceeded the free tier limit.
- **GitHub tool errors** — make sure your `GITHUB_TOKEN` has the `repo` scope and hasn't expired.
- **Filesystem MCP can't read a path** — the container only mounts `/srv`. Requests outside that directory will fail by design.
- **Gerald Tools isn't being called** — check that the Gerald Tools workflow is activated and the tool description in Gerald Main matches exactly.

---

## MCP Server Reference

| Server | Port | Source |
|--------|------|--------|
| Filesystem | 8881 | [github.com/modelcontextprotocol/servers — filesystem](https://github.com/modelcontextprotocol/servers/tree/main/src/filesystem) |
| Brave Search | 8882 | [github.com/modelcontextprotocol/servers — brave-search](https://github.com/modelcontextprotocol/servers/tree/main/src/brave-search) |
| GitHub | 8883 | [github.com/github/github-mcp-server](https://github.com/github/github-mcp-server) |
| Ollama | 8884 | [github.com/ergut/mcp-ollama-server](https://github.com/ergut/mcp-ollama-server) |
| Postgres | 8885 | [github.com/modelcontextprotocol/servers — postgres](https://github.com/modelcontextprotocol/servers/tree/main/src/postgres) |

All servers are exposed as SSE endpoints via [Supergateway](https://github.com/supercorp-ai/supergateway).

---

## What's Next?

More episodes are on the way — voice input/output, image generation, Home Assistant integration, and more.

🎬 [Watch the YouTube series](https://youtube.com/@NathServer) to stay up to date.
