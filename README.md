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

- `site.json` — siteId, name, tagline, theme tokens, right-rail links, optional `nests[]`, sample seed/notifs/threads (sample copy is **not** mixed into the live feed). Stories v0 is flagged here (`stories.enabled`); **only samochat** turns it on in this repo.
- `factory.js` — Auth email/password, live posts, image upload, poll, reply, delete, empty-state, nest path rooms (`/{slug}`), feature-flagged Stories tray/viewer/create
- `_redirects` — Cloudflare Pages SPA fallback so `/{slug}` serves this shell. GitHub Pages ignores it; `404.html` bounces the path back into the shell.
- `firestore.rules`, `storage.rules`, `firebase.indexes.json`, `RULES.md` — source of truth; publish in the Firebase console for **subx-skins**. Do not `firebase deploy` from an agent.

## Nest rooms (schema v0)

`site.json` may include optional `nests`:

```json
"nests": [
  {
    "slug": "wine",
    "label": "Wine",
    "parent": null,
    "kind": "category",
    "blurb": "…",
    "nav": true,
    "rankings": [],
    "reviews": [],
    "railPins": []
  }
]
```

URL grammar: apex `/` is the home room. `/{slug}` is the same shell, filtered by optional post field `nestSlug`. Hash routes (`#home`, `#explore`, …) still work. v0 is one path segment; reserved names (`explore`, `news`, `factory.js`, …) are not nests.

Quiet nests stay URL-addressable. Left nav / Explore only list nests with activity (`nestSlug` on a live post) or `nav: true`. Set `nav: false` to hide even then.

**Static hosting:** nest deep links need an SPA fallback (Cloudflare Pages `_redirects`: `/* /index.html 200`) or the `404.html` bounce. Keep asset hrefs relative (`factory.js`, `site.json`) so `/wine` (no trailing slash) still loads `/factory.js`.

samochat’s `nests` stub (`pier`, `promenade`, `montana`) is dummy smoke data — do not stamp it onto other skins.

## Stamp to another clone

Copy `factory.js` + cache-bust `index.html` (`factory.js` / `site.json` query) + `_redirects` + `404.html`. Put that skin’s nest tree in **its** `site.json`. Publish the `nestSlug` composite index in Firebase by hand. Preview banner + `noindex` stay until Jebb lifts GTM.

## Product locks

- Guest is browse-only. Google provider stays off until enabled.
- Images `image/*` ≤ 5 MB. GIF is a user-uploaded `.gif`, not Tenor.
- AI off the hot path. No Reddit/X ingest.
- Preview banner and `robots.txt` noindex stay until Jebb lifts them.
