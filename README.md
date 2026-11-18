# Erecta patient-support WhatsApp bot (Vercel)

Start with **SETUP-GUIDE.md**.

| Page | What it is |
| --- | --- |
| `/` | landing page with "Chat privately on WhatsApp" CTA and QR code |
| `/#chat` | test the bot in the browser |

| Endpoint | Used by |
| --- | --- |
| `/api/whatsapp` | Meta WhatsApp webhook for the brand's own number |
| `/api/chat` | the browser test chat |
| `/api/reports?key=...` | side-effect reports and requests for a person (drug-safety team) |
| `/api/config` | the landing page |

Code: `lib/agent.js` (conversation, tools, reports), `lib/content.js` (approved content, safety checks,
retrieval), `lib/store.js` (Upstash Redis), `lib/whatsapp.js`. Content: `public/data/erecta.json` (draft).
