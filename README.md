> ⚠️ **Note:** You may notice `.env` files in this repo — they contain completely generic placeholder values. None of these credentials are used on my actual server.

# Gerald 2.0

A fully self-hosted AI assistant built with n8n and Ollama. No APIs, no cloud, no subscriptions — just your hardware.

Gerald started as a personal project to replace cloud-based AI agents with something that runs entirely locally. This repo contains everything you need to build your own version, episode by episode.

## What Gerald Can Do

- **Chat naturally** via a custom PWA web interface or n8n's built-in chat
- **Control your server** via SSH (run commands, check health, manage files)
- **Write and run code** through a dedicated Gerald Code sub-agent
- **Look up information** via Wikipedia
- **Remember conversations** using Postgres-backed memory

### Coming Soon

- Voice input/output — talk to Gerald from your phone
- Image generation via ComfyUI
- Home Assistant integration — control your smart home
- Web browsing and research
- Multi-agent workflows with sub-agents

## Tech Stack

| Component | Purpose |
|-----------|---------|
| **n8n** | Workflow automation + AI agent orchestration |
| **Ollama** | Local LLM inference (Llama 3.2, Qwen, etc.) |
| **Postgres** | Memory storage + conversation history |
| **Docker** | Container orchestration |

## Episodes

| Episode | What You'll Build |
|---------|-------------------|
| **[Episode 1](./Episode1)** | Base setup: n8n + Ollama + SSH tool + Wikipedia |
| **[Episode 2](./Episode%202)** | Multi-agent setup: Gerald Code sub-agent + Postgres memory |
| **[Episode 3](./Episode%203)** | Custom WebUI: PWA chat interface for Gerald |

---

## Episode 1 — Base Setup

Get n8n, Ollama, and Postgres running in Docker and wire up Gerald's first tools.

**Files:** `Gerald Main.json`, `AI Server SSH Tool.json`, `docker-compose.yml`

```bash
# Edit docker-compose.yml, then start the stack
sudo nano docker-compose.yml
sudo docker compose up -d

# Create the initial memory table
sudo tee init.sql << 'EOF'
CREATE TABLE IF NOT EXISTS gerald_memory (
  id SERIAL PRIMARY KEY,
  key TEXT UNIQUE NOT NULL,
  value TEXT,
  updated_at TIMESTAMP DEFAULT NOW()
);
EOF

# Open n8n and import the workflow JSON files
# http://localhost:5678
```

---

## Episode 2 — Multi-Agent Setup

Upgrade Gerald with a dedicated code sub-agent and persistent Postgres memory.

**Files:** `Gerald Main ep2.json`, `Gerald Code ep2.json`, `AI Server SSH Tool ep2.json`

```bash
# Pull the models used in this episode
ollama pull qwen3.5:9b
ollama pull qwen3.5:2b

# Create the memory tables
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

# Verify the tables were created
sudo docker compose exec db psql -U n8n -d n8n -c "\dt gerald*"

# Restart the stack to pick up any config changes
sudo docker compose down
sudo docker compose up -d
```

---

## Episode 3 — Custom WebUI

Build a polished PWA chat interface that talks directly to Gerald's n8n webhook — no more using n8n's built-in chat.

**Files:** `index.html`

### Setup

1. **Edit the webhook URL** — open `index.html` and update the `WEBHOOK_URL` constant near the top of the `<script>` block to point at your n8n instance:

   ```js
   const WEBHOOK_URL = "http://<your-server-ip>:5678/webhook/<your-webhook-id>";
   ```

2. **Serve the file** — open a terminal in the folder containing `index.html` and run:

   ```bash
   python3 -m http.server 8080
   ```

3. **Open the UI** in your browser:

   ```
   http://localhost:8080
   ```

   On mobile (same network), replace `localhost` with your machine's local IP address.

### Webhook Testing

If the UI isn't getting a response, test the webhook directly with curl:

```bash
curl -X POST http://192.168.4.115:5678/webhook/983852e5-8c44-42d3-a61c-e00c93d6d1f3 \
  -H "Content-Type: application/json" \
  -d '{"chatInput": "hello", "sessionId": "test-123"}'
```

> Replace the IP and webhook ID with your own. A valid response here confirms the n8n workflow is running and reachable — the issue is elsewhere (CORS, wrong URL in `index.html`, etc.).

### Installing as a PWA

The UI ships as a Progressive Web App. On mobile:

- **iOS Safari:** tap Share → *Add to Home Screen*
- **Android Chrome:** tap the menu → *Add to Home Screen* / *Install App*

Once installed it opens full-screen with no browser chrome, just like a native app.

---

## Why Gerald?

I got tired of:
- Paying monthly for AI tools that phone home
- Trusting cloud providers with my data
- Waiting for API rate limits
- Complex "agent frameworks" that need a PhD to configure

Gerald is the opposite. It's n8n workflows you can see and edit, running models you own, on hardware you control.

## YouTube Series

This repo follows along with my YouTube series where I build Gerald from scratch:

🎬 [Watch the series](https://youtube.com/@NathServer)

## Support

- 📸 Instagram: [@NathsServer](https://www.instagram.com/NathsServer)
- 🎬 YouTube: [@NathsServer](https://www.youtube.com/@NathsServer)

If Gerald helps you build something cool, let me know — I'd love to see it.

---

**No cloud. No APIs. Just your AI.**
