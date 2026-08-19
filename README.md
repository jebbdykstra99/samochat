# samochat

Santa Monica geo chat. People talking about SAMO.

This is a **static** dress rehearsal of the bakasan SubX chrome (three-column X-like shell: left nav, center feed, right rail, hash routes, sign-in modal, mobile hamburger) — the same shell used for 415chat, restyled for Santa Monica. It is **not** the FastAPI / Next `subx` stack. No React, no Next, no FastAPI, no Firebase, no model calls.

Wordmark: **samochat**. Tagline: *Santa Monica, talking.*

## GitHub Pages + custom domain

These files are meant to drop into an empty public repo and be served from GitHub Pages at **samochat.com**.

1. Push this folder’s contents to branch `main` (site root, not `/docs`).
2. Repo **Settings → Pages**: Deploy from branch `main` / `/` (root).
3. Custom domain: `samochat.com`. The `CNAME` file in this repo already contains exactly that.

**DNS at GoDaddy still needs to point at GitHub Pages.** Do not change DNS from this repo. Typical GitHub Pages records:

- Apex `A` records to `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
- or a `CNAME` for `www` to `jebbdykstra99.github.io`

Until DNS is pointed, Pages will serve on the github.io URL only if the repo is project-pages configured; for the custom domain, use a user/org Pages root as above.

## What this is / is not

- Feed-first dummy posts about Santa Monica (pier, Promenade, Montana, Ocean Park, beach bike path, sunset). Fake handles only.
- Ranking chrome (For You / Following / Hot / New) is UI only.
- Sign-in modal closes (X, Escape, overlay click); auth is stubbed locally. No bakasan Firebase project keys.
- No AskAI. No cross-post to X or Reddit. We are not X.com.
- Distinct from 415chat (no Golden Gate orange, no fog/BART/Giants copy) and from bakasan (no paintings, no parchment/gold).
