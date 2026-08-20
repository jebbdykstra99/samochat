# samochat

Santa Monica geo chat. People talking about SAMO.

Static GitHub Pages shell (`index.html` + `styles.css` + `factory.js` + `site.json`) on Firebase project **subx-skins**. Not the FastAPI / Next `subx` stack. Not bakasan-art.

Wordmark: **samochat**. Tagline: *Santa Monica, talking.* `SITE_ID` is `samochat`.

## GitHub Pages + custom domain

1. Push to branch `main` (site root, not `/docs`).
2. Repo **Settings → Pages**: Deploy from branch `main` / `/` (root).
3. Custom domain: `samochat.com`. `CNAME` already contains that.

**DNS at GoDaddy still needs to point at GitHub Pages.** Do not change DNS from this repo.

## Factory files

- `site.json` — siteId, name, tagline, theme tokens, right-rail links, sample seed/notifs/threads (sample copy is **not** mixed into the live feed)
- `factory.js` — Auth email/password, live posts, image upload, poll, reply, delete, empty-state
- `firestore.rules`, `storage.rules`, `firebase.indexes.json`, `RULES.md` — source of truth; publish in the Firebase console for **subx-skins**. Do not `firebase deploy` from an agent.

## Product locks

- Guest is browse-only. Google provider stays off until enabled.
- Images `image/*` ≤ 5 MB. GIF is a user-uploaded `.gif`, not Tenor.
- AI off the hot path. No Reddit/X ingest.
- Preview banner and `robots.txt` noindex stay until Jebb lifts them.
