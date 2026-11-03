# Zucchy AI

The website for Zucchy AI, an AI-powered gym. It's a single page (`index.html`) with five sections that each have their own link:

| Page | Link |
|---|---|
| Home | `index.html` |
| Gym World: zones, equipment, facilities | `index.html#gym-world` |
| Gym Tricks: tips, motivation, weekly plan | `index.html#gym-tricks` |
| Success Stories | `index.html#success-stories` |
| Our Instructors | `index.html#instructors` |

Every **Register Here** and **Book a session** button opens the **Zucchy AI chat**. So do the round chat button in the bottom-right corner, clicking the robot on the home page, and a link to `index.html#chat`.

## The chat and the n8n webhook

Each message is sent as a `POST` with JSON to the n8n webhook set in `WEBHOOK_URL` in `index.html`:

```json
{
  "action": "sendMessage",
  "sessionId": "same id for every message from one visitor",
  "chatInput": "the visitor's message",
  "message": "the visitor's message",
  "page": "home | gym-world | gym-tricks | success-stories | instructors",
  "source": "zucchy-ai-website",
  "sentAt": "2026-11-03T10:30:00.000Z"
}
```

Whatever the workflow sends back is shown as Zucchy AI's reply. `{"output": "..."}` works (the n8n AI Agent's default), as do `reply`, `text`, `message`, a list of these, or plain text. Replies can use **bold**, *italics*, bullet lists and links. If the workflow sends nothing back, the visitor sees "Thanks! Your message has been sent…".

What to set in n8n:

- **Webhook node:** HTTP Method `POST`. Under Options, set **Allowed Origins (CORS)** to `*` or your site's address, or browsers will block the messages.
- **Respond:** `Using 'Respond to Webhook' Node` (or `When Last Node Finishes`), so the AI's answer comes back to the chat.
- **In an AI Agent node:** the message is `{{ $json.body.chatInput }}`. Use `{{ $json.body.sessionId }}` as the memory session key so it remembers the conversation.
- **Activate the workflow.** The site uses the production `/webhook/` URL, not `/webhook-test/`.

## Viewing it

Open `index.html` in a browser. Nothing needs to be installed. To put it online with GitHub Pages, go to **Settings → Pages**, choose **Deploy from a branch**, and pick `main` and `/ (root)`.

## Files

- `index.html`: the whole site. Your own gym photos are built into it, so it works on its own.
- `images/`: the original copies of those photos, kept for future edits.

Some photos on the inner pages load from [Unsplash](https://unsplash.com) (free under the Unsplash License). If one can't load, one of the built-in gym photos shows instead.

## Before going live

- **Success Stories** and **Our Instructors** contain sample names, quotes, results and bios. Replace them with real members' stories (with their permission) and your real coaches.
- Check the **Gym World** facilities list (sauna, smoothie bar, parking and so on) matches what the gym actually offers.
- Send a test message through the chat on the live site and confirm it arrives in n8n and a reply comes back.
