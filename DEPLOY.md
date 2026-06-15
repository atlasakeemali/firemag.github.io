# FIRE!!! — Call for Proposals · Deploy guide

A single static page. No build step, no framework, no server code.

## 1. What to deploy

Deploy the **`website/`** folder. The page is:

- **`FIRE Call for Proposals.html`** ← the deployable file (lean, relative-path assets)

Rename it to **`index.html`** at deploy time (or configure your host to serve it as the root document) so the site loads at `/`.

> `FIRE Call for Proposals (offline).html` is a **backup only** — a single self-contained file (all images/fonts inlined) for emailing or opening with no web server. Do **not** deploy it; it's ~10 MB and exists so the page survives offline.

### Files that must ship together

```
index.html              (renamed from "FIRE Call for Proposals.html")
assets/
  favicon.svg
  og-card.svg                       social share image
  fire-1926-cover.png               1926 archive plate
  FIRE-Call-for-Proposals.pdf       the printable Call (both "Download the Call" buttons)
  seq/                              the 51 lineage plates (plate-00 … plate-50)
DEPLOY.md                           (this file — does not need to ship)
```

Keep the `assets/` folder next to the HTML; every reference is a relative path.

## 2. Fill in the submission endpoint (required to receive proposals)

The form needs one value. Open the HTML and find, near the top of the first `<script>`:

```js
const SUBMIT_ENDPOINT = "__REPLACE_ME__";
```

Paste your form-handler URL between the quotes. Any standard handler works:

| Handler | Endpoint looks like |
|---|---|
| Formspree | `https://formspree.io/f/XXXXXXXX` |
| Basin | `https://usebasin.com/f/XXXXXXXX` |
| Google Apps Script web app | `https://script.google.com/macros/s/XXXX/exec` |

That constant is the **only** thing to edit — the form syncs its no-JS fallback `action` to the same value automatically.

> **Never** put API keys, tokens, or private addresses in the HTML. A handler URL is all that belongs here; real credentials live in the handler's own dashboard.

### Deliver to submissions@firemag.org + autoresponder

- **Formspree:** Form → Settings → add `submissions@firemag.org` as a recipient; enable **Autoresponse** so the submitter gets a confirmation (their address arrives in the `email` field).
- **Basin:** Form → Settings → Notifications → send to `submissions@firemag.org`; turn on the **Autoresponder**.
- **Apps Script:** in `doPost(e)`, `MailApp.sendEmail` to `submissions@firemag.org` (the new proposal) and to `e.parameter.email` (the confirmation).

The form POSTs these fields: `name`, `email`, `section`, `piece_form`, `about_you`, `what_making`, `why_fire`, `work_sample`, `confirm_descendant`, `confirm_age`, `confirm_unpublished`, `human_made`, plus a `company` honeypot (always empty — ignore/treat as spam if filled) and `form_loaded_at`.

## 3. Email / domain prerequisites

- A mailbox or alias at **`submissions@firemag.org`** that can receive (and, for the autoresponder, send) mail.
- The **`firemag.org`** domain pointed at your host (DNS A/CNAME or nameservers per the host's instructions).
- For reliable autoresponder delivery, add the handler's recommended **SPF/DKIM** records for your domain.
- Update `<meta property="og:url" content="https://firemag.org/">` if you deploy under a different URL.

## 4. Recommended host

Any static host works. Easiest, free tier, HTTPS + custom domain included:

1. **Cloudflare Pages** — drag-and-drop or Git; fast global CDN.
2. **Netlify** — drag the `website/` folder onto the dashboard; set the custom domain.
3. **Vercel** — import the folder/repo; framework preset “Other”.

No environment variables or build command are required — output directory is the folder itself.

## 5. Pre-launch checklist

- [ ] Renamed the deploy file to `index.html`.
- [ ] `SUBMIT_ENDPOINT` set to a real handler URL.
- [ ] Handler delivers to `submissions@firemag.org` and autoresponds to the submitter.
- [ ] Sent a test proposal end-to-end (success screen shows, email arrives).
- [ ] `assets/` folder uploaded alongside the HTML (favicon, og-card, PDF, 51 plates).
- [ ] Custom domain + HTTPS live; `og:url` matches the deployed URL.
