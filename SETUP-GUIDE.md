# Setup guide: Erecta patient-support WhatsApp bot on Vercel

This project is **Project 2**: a private WhatsApp assistant for Erecta patients, on the brand's **own**
WhatsApp Business number, with a landing page, CTA button and QR code.

What it does:

- Greets patients discreetly and answers **only** from the approved content in `public/data/erecta.json`.
- Checks every message for emergencies (chest pain, fainting, an erection over 4 hours, sudden vision or
  hearing loss, trouble breathing) and replies with the local emergency number first. These checks are
  built into the code and run before the AI.
- Refuses to discuss the medicine with anyone who says they are under 18.
- Records every side effect a patient mentions for the drug-safety team, and can forward it automatically.
- Offers a confidential doctor consultation with booking links to the doctor microsites.
- Lets a patient ask for a person; requests are logged for the support team.
- Never gives doses, prices, discounts or where to buy.

> **Before going live:** `erecta.json` is a **draft** written from general sildenafil safety information.
> Replace it with your medically, legally and regulatory-approved patient information. Promotion of
> prescription medicines is restricted in India and Kenya, so regulatory must approve where the QR code
> and page are used.

> **Vercel plan:** the free Hobby plan is for personal, non-commercial use only; use Pro for the real service.

---

## Step 1: GitHub (5 minutes)

New **private** repository `erecta-whatsapp-bot` > **uploading an existing file** > drag everything inside
the unzipped `erecta-whatsapp-bot-vercel` folder > **Commit changes**.

## Step 2: Vercel (3 minutes)

**Add New... > Project** > import `erecta-whatsapp-bot` > Framework Preset **Other** > **Deploy**.
The landing page opens at `https://<your-bot>.vercel.app`.

## Step 3: Storage (3 minutes)

**Storage** tab > **Upstash for Redis** (free plan, region near your patients) > connect to this project
> **Redeploy**. Without it, side-effect reports are not saved and the page says so.

## Step 4: AI and safety settings (5 minutes)

**Settings > Environment Variables**:

| Name | Value |
| --- | --- |
| `LLM_API_KEY` | OpenAI or Gemini key |
| `LLM_MODEL` | default `gpt-6-luna`; for Gemini a current Flash model |
| `LLM_BASE_URL` | Gemini only: `https://generativelanguage.googleapis.com/v1beta/openai` |
| `ADMIN_KEY` | a long password for the reports page |
| `SAFETY_WEBHOOK_URL` | optional: an n8n, Make, Zapier or Teams/Slack workflow address that receives each side-effect report |
| `BRAND_TEAM_NAME` | optional, default `Erecta patient support` |

**Redeploy**, then test at `https://<your-bot>.vercel.app/#chat`.

## Step 5: The brand's own WhatsApp number (20 minutes to test, longer to go live)

Use a **separate** number from the clinic booking assistant.

1. In Meta for Developers, use your app (or create one) > **WhatsApp > API Setup**. Add a **second phone number**
   for the brand, or use the test number while testing. Note its **Phone number ID** and a token.
2. In Vercel add `WHATSAPP_TOKEN`, `WHATSAPP_PHONE_NUMBER_ID`, `WHATSAPP_VERIFY_TOKEN` (make one up),
   `WHATSAPP_APP_SECRET` and `WHATSAPP_DISPLAY_NUMBER` (digits only, e.g. `919800000000`). **Redeploy.**
3. In Meta, **WhatsApp > Configuration > Webhook**: Callback URL `https://<your-bot>.vercel.app/api/whatsapp`,
   the same verify token, then subscribe to **messages**.
4. For launch: complete business verification, get the display name "Erecta patient support" (or what
   regulatory approves) approved, add the brand logo, description, privacy policy link and support hours,
   and create a permanent token.

The CTA button and QR code on the landing page update to the new number automatically.

## Side-effect reports and requests for a person

Open `https://<your-bot>.vercel.app/api/reports?key=<ADMIN_KEY>` for the latest 200 side-effect reports and
handover requests. Anyone with this link can see them, so share it only with the drug-safety team. Reports are
also kept in the Upstash console, and sent to `SAFETY_WEBHOOK_URL` if set. Agree a daily review routine with
your drug-safety team and put their contact in `safety_team_contact` in `erecta.json`.

## Editing the content

Everything the bot may say about Erecta is in `public/data/erecta.json`:

| Field | What it is |
| --- | --- |
| `opening_message` | the first reply |
| `facts`, `safety` | approved patient information |
| `service_faq` | privacy, cost, "am I talking to a doctor", prescriptions |
| `consultation` | doctors to book with and their links. Set `booking_url` to your doctor microsites address, e.g. `https://doctor-microsites.vercel.app/#dr-mathew` |
| `handover_message` | what patients are told when they ask for a person |

Edit on GitHub and commit; Vercel redeploys by itself.
