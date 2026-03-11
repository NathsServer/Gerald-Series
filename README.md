# Gerald 2.0

A fully self-hosted AI assistant built with n8n and Ollama. No APIs, no cloud, no subscriptions — just your hardware.

Gerald started as a personal project to replace cloud-based AI agents with something that runs entirely locally. This repo contains everything you need to build your own version, episode by episode.

## What Gerald Can Do

- **Chat naturally** via n8n's built-in chat interface
- **Control your server** via SSH (run commands, check health, manage files)
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
| **Episode 2** | *Coming soon* |
| **Episode 3** | *Coming soon* |

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

- 📸 Instagram: [@NathServer](https://instagram.com/NathServer)
- 🎬 YouTube: [@NathServer](https://youtube.com/@NathServer)

If Gerald helps you build something cool, let me know — I'd love to see it.

---

**No cloud. No APIs. Just your AI.**
