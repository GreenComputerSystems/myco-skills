---
name: myco
description: >
  Connect a website to the Myco discovery network (myco.stream) — hosted
  comments, analytics, live counters, and a directory listing from one script
  tag. Use when the user asks to install Myco, add Myco integration, add a
  Myco content type (music, video, articles, art, photography, podcasts,
  business), update the Myco manifest after content changes, or validate an
  existing Myco integration.
---

# Myco Integration Skill

You integrate websites with Myco, the open discovery network. Everything you
need lives at fetchable URLs — fetch only what the current job requires; never
fetch capability docs for content the site doesn't have.

## Commands

Interpret the user's request as one of four modes:

### `install` (default — "add Myco to my site")

1. Fetch https://myco.stream/llms/core.md and follow it exactly. It contains
   the network rules, the information-gathering requirements, and a **strict
   halt condition**: you MUST present a Discovery Report and wait for the
   user's "CONFIRMED" before writing any files.
2. Inventory the site's content types. For each type that exists, fetch ONLY
   the matching doc:
   - Music: https://myco.stream/llms/music.md
   - Video: https://myco.stream/llms/video.md
   - Articles/blog/news: https://myco.stream/llms/articles.md
   - Art: https://myco.stream/llms/art.md
   - Photography: https://myco.stream/llms/photography.md
   - Podcasts: https://myco.stream/llms/podcasts.md
   - Business (local/app/SaaS): https://myco.stream/llms/business.md
3. Fetch https://myco.stream/llms/engagement.md and add injection points
   (comments, social buttons, player if audio content) and counters.
4. If the site has images/audio to reference, fetch
   https://myco.stream/llms/assets.md and conform.
5. Ask the owner for their claim code (myco.stream dashboard shows one
   account-wide code) and set `node.claim_code` — without it their analytics
   dashboard stays locked.
6. Finish with core.md's validation checklist and its "seeds" section
   (MYCO.md download, README warning, build tripwire).
7. After deploy, verify live: `GET https://api.myco.stream/api/validate?domain=<domain>`
   — fix every `fail` in `checks[]`, re-run, then give the owner their status
   link: `https://myco.stream/check?domain=<domain>`.

### `add <content-type>` ("add my podcast to Myco")

Site already integrated. Read `MYCO.md` at the project root first. Fetch only
the capability doc for the new type, add its manifest section, add
`node.content_types` entry, add data attributes to the relevant pages, re-run
validation, then update `MYCO.md` to record the new content type and attributes.

### `update` ("I added blog posts, sync Myco")

Read `MYCO.md` at the project root first — it is the source of truth for this
site's Myco setup. Fetch nothing unless unsure of a field. Update
`myco.config.json` (static) or verify the dynamic route covers the new content
(database). Rebuild, confirm `/.well-known/myco.json` reflects the change, and
update `MYCO.md` if anything about the integration changed.

### `validate` ("check my Myco integration")

Two passes:

1. **Live** (if the site is deployed): `GET https://api.myco.stream/api/validate?domain=<domain>`
   returns `{ ok, summary, checks: [{ id, label, status: pass|fail|warn, detail }] }`
   — the network's own view: script tag, manifest, schema, attribution,
   registration. Report it, fix every `fail`. Owner-friendly version of the
   same report: `https://myco.stream/check?domain=<domain>`.
2. **Static**: fetch https://myco.stream/llms/core.md, run its validation
   checklist against the project, report pass/fail per item with fixes.

Confirm a `MYCO.md` exists at the project root and matches the live manifest;
create or correct it from https://myco.stream/sdk/MYCO.md if missing or stale.
Also check the browser console on a live page: myco.js logs a collapsed
"integration report" (containers found, counters wired vs orphaned — orphaned
means slug mismatch with the manifest).

## Hard rules (apply in every mode)

- ALWAYS keep a `MYCO.md` at the project root (template: https://myco.stream/sdk/MYCO.md). Read it before changing the site; update it after. It is this site's Myco integration spec — node identity, manifest location, content types, `data-myco-*` attributes, asset conventions, and the ownership `claim_code`.
- ALWAYS hotlink the script: `<script src="https://myco.stream/myco.js" async></script>`. NEVER download or vendor myco.js — a local copy freezes the site on a stale version. Delete any vendored copy you find.
- NEVER fabricate real-world data (names, phones, addresses, hours). Ask.
- NEVER declare third-party content. One node = one site's own content.
- Custom domains only; subdomains roll up to the root domain.
- All manifest paths relative; plain text in all text fields.
- Full schema when field-level detail is needed: https://myco.stream/sdk/types.ts
- FAQ and doc index: https://myco.stream/llms.txt
