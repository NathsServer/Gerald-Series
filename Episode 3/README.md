# Episode 3 — Custom WebUI

> 👈 **New here?** Start with the [main README](../README.md) for a full overview of the Gerald project, what it does, and what you'll be building across all episodes.
>
> **Coming from Episode 2?** Make sure you have the [Episode 2](../Episode%202/README.md) multi-agent setup running before continuing.

This episode replaces n8n's built-in chat window with a polished **Progressive Web App (PWA)** — a single HTML file you can open in any browser or install to your phone's home screen like a native app.

By the end of this episode you'll have:
- A clean, dark, terminal-inspired chat UI served from your local machine
- A fully installable PWA that works on mobile and desktop
- No more opening n8n just to talk to Gerald

---

## Files in This Folder

| File | What It Is |
|------|-----------|
| `index.html` | The complete PWA — all HTML, CSS, and JS in a single file |
| `Gerald Main.json` | Updated Gerald workflow with a webhook trigger for the new UI |
| `Gerald Code.json` | Updated Gerald Code sub-agent workflow |
| `AI Server SSH Tool.json` | SSH tool workflow (unchanged from Episode 2, included for reference) |
| `Claude Prompt` | The prompt used to generate `index.html` — useful if you want to customise the UI |

---

## Step-by-Step Setup

### 1. Import the updated workflows

Inside n8n, import and activate:
1. `Gerald Main.json`
2. `Gerald Code.json`

Deactivate the old Episode 2 versions to avoid duplicate webhook conflicts.

### 2. Get your webhook URL from n8n

1. Open the **Gerald Main** workflow in n8n
2. Click the **Chat / Webhook** trigger node
3. Copy the **Production webhook URL** — it will look something like:
   ```
   http://<your-server-ip>:5678/webhook/<your-webhook-id>
   ```

### 3. Update the webhook URL in `index.html`

Open `index.html` in a text editor and find the `WEBHOOK_URL` constant near the top of the `<script>` block:

```js
const WEBHOOK_URL = "http://<your-server-ip>:5678/webhook/<your-webhook-id>";
```

Replace it with your actual URL from Step 2.

### 4. Serve the file

Open a terminal in this folder and run:

```bash
python3 -m http.server 8080
```

Then open `http://localhost:8080` in your browser. On mobile (same network), replace `localhost` with your machine's local IP address.

---

## Installing as a PWA

The UI is a Progressive Web App — you can install it to your phone's home screen for a native-app feel:

- **iOS Safari:** tap Share → *Add to Home Screen*
- **Android Chrome:** tap the menu → *Add to Home Screen* / *Install App*

Once installed it opens full-screen with no browser chrome.

---

## Troubleshooting

**UI sends a message but gets no response:**

Test the webhook directly with curl to isolate whether the problem is n8n or the UI:

```bash
curl -X POST http://<your-server-ip>:5678/webhook/<your-webhook-id> \
  -H "Content-Type: application/json" \
  -d '{"chatInput": "hello", "sessionId": "test-123"}'
```

- ✅ **Curl works, UI doesn't** — the issue is the `WEBHOOK_URL` in `index.html` or a CORS setting
- ❌ **Curl also fails** — the n8n workflow isn't active or the webhook URL is wrong

**"Gerald is offline" shown in the UI:**

The service worker shows this when it can't reach the server. Check that n8n is running and the webhook URL is correct.

---

## Customising the UI

The `Claude Prompt` file contains the exact prompt used to generate `index.html`. If you want to make significant changes to the design or behaviour, you can feed this prompt (plus your modifications) to an AI to regenerate the file.

For small tweaks, just edit `index.html` directly — everything is in one file with clear comments.

---

## What's Next?

More episodes are on the way — voice input/output, image generation, Home Assistant integration, and more.

🎬 [Watch the YouTube series](https://youtube.com/@NathServer) to stay up to date.
