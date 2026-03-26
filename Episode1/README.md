# Episode 1 — Base Setup

> 👈 **New here?** Start with the [main README](../README.md) for a full overview of the Gerald project, what it does, and what you'll be building across all episodes.

This episode gets the core stack running: **n8n**, **Ollama**, and **Postgres** in Docker, with Gerald's first two tools — SSH server control and Wikipedia lookup.

By the end of this episode Gerald will be able to:
- Answer questions and chat naturally inside n8n
- Run commands on your server over SSH
- Look up information via Wikipedia

---

## Files in This Folder

| File | What It Is |
|------|-----------|
| `docker-compose.yml` | Defines the Docker stack (n8n + Ollama + Postgres) |
| `.env` | Environment variables for the stack — **edit this with your own values** |
| `Gerald Main.json` | The main Gerald n8n workflow — import this into n8n |
| `AI Server SSH Tool.json` | The SSH tool sub-workflow — import this into n8n |
| `Commands Guide` | Quick reference of the terminal commands used in this episode |

---

## Step-by-Step Setup

### 1. Configure your environment

Open `.env` and set your own values — at minimum change the Postgres password:

```env
POSTGRES_USER=n8n
POSTGRES_PASSWORD=your-secure-password-here
POSTGRES_DB=n8n
GENERIC_TIMEZONE=Europe/London
```

### 2. Start the Docker stack

```bash
sudo docker compose up -d
```

This starts n8n, Ollama, and Postgres. Give it a minute on first run — Ollama may need to pull its image.

### 3. Create the memory table

```bash
sudo tee init.sql << 'EOF'
CREATE TABLE IF NOT EXISTS gerald_memory (
  id SERIAL PRIMARY KEY,
  key TEXT UNIQUE NOT NULL,
  value TEXT,
  updated_at TIMESTAMP DEFAULT NOW()
);
EOF
```

### 4. Open n8n

Navigate to `http://localhost:5678` (or your server's IP) and complete the initial setup.

### 5. Import the workflows

Inside n8n:
1. Go to **Workflows → Import**
2. Import `Gerald Main.json`
3. Import `AI Server SSH Tool.json`
4. Activate both workflows

### 6. Configure credentials in n8n

You'll need to set up:
- **Postgres** connection (use the values from your `.env`)
- **SSH** credentials for your server

Follow along with the video for the exact credential configuration.

---

## Troubleshooting

- **n8n won't start** — check `sudo docker compose logs n8n` for errors
- **Postgres connection fails** — make sure your `.env` values match what you entered in n8n's Postgres credential
- **SSH tool errors** — confirm your SSH credentials are correct and the target machine is reachable

---

## What's Next?

➡️ **[Episode 2](../Episode%202/README.md)** — Upgrade Gerald with a dedicated code sub-agent and persistent conversation memory
