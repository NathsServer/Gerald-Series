# Episode 2 — Multi-Agent Setup

> 👈 **New here?** Start with the [main README](../README.md) for a full overview of the Gerald project, what it does, and what you'll be building across all episodes.
>
> **Coming from Episode 1?** Make sure you have the [Episode 1](../Episode1/README.md) stack running before continuing.

This episode upgrades Gerald with a dedicated **Gerald Code** sub-agent and replaces the simple key-value memory with proper **Postgres-backed conversation history** — one table per agent.

By the end of this episode Gerald will be able to:
- Hand off coding tasks to a specialist sub-agent (Gerald Code)
- Remember separate conversation histories for each agent
- Run code directly on your server via SSH

---

## Files in This Folder

| File | What It Is |
|------|-----------|
| `Gerald Main ep2.json` | Updated main Gerald workflow — import this into n8n |
| `Gerald Code ep2.json` | The new Gerald Code sub-agent workflow — import this into n8n |
| `AI Server SSH Tool ep2.json` | Updated SSH tool workflow — import this into n8n |
| `init.sql` | SQL to create the new memory tables manually (alternative to the commands below) |
| `Commands & System Prompts.md` | All terminal commands and the system prompts used in this episode |
| `sudoers.d` | Optional: gives n8n passwordless sudo access on your server |

---

## Step-by-Step Setup

### 1. Pull the required models

```bash
ollama pull qwen2.5:7b
ollama pull qwen2.5:0.5b
```

> Check the video for the exact model names used — these may be updated between episodes.

### 2. Create the Postgres memory tables

This replaces the old `gerald_memory` table with two separate tables — one for each agent:

```bash
sudo docker compose exec db psql -U n8n -d n8n -c "
DROP TABLE IF EXISTS gerald_memory;
CREATE TABLE IF NOT EXISTS gerald_server (
  id SERIAL PRIMARY KEY,
  session_id VARCHAR(255) NOT NULL,
  message JSONB NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
CREATE TABLE IF NOT EXISTS gerald_code (
  id SERIAL PRIMARY KEY,
  session_id VARCHAR(255) NOT NULL,
  message JSONB NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
"
```

### 3. Verify the tables were created

```bash
sudo docker compose exec db psql -U n8n -d n8n -c "\dt gerald*"
```

You should see `gerald_server` and `gerald_code` listed.

### 4. Restart the stack

```bash
sudo docker compose down
sudo docker compose up -d
```

### 5. Import the updated workflows into n8n

Inside n8n, import (and activate) all three JSON files:
1. `Gerald Main ep2.json`
2. `Gerald Code ep2.json`
3. `AI Server SSH Tool ep2.json`

Deactivate or delete the old Episode 1 versions to avoid conflicts.

### 6. (Optional) Set up passwordless sudo for n8n

If you want Gerald Code to run privileged commands on your server without a password prompt, follow the instructions in the `sudoers.d` file.

> ⚠️ This grants broad sudo access. Read the file carefully and restrict the permissions to only what you need.

---

## Troubleshooting

- **Table creation fails** — make sure Postgres is running: `sudo docker compose ps`
- **Gerald Code isn't being called** — check that the sub-agent workflow is activated and the tool description in Gerald Main matches
- **Memory not persisting** — confirm the Postgres credential in n8n matches your `.env` values

---

## What's Next?

➡️ **[Episode 3](../Episode%203/README.md)** — Build a polished PWA chat interface so you can talk to Gerald from any device on your network
