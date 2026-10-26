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

- `site.json`: siteId, name, tagline, compose/empty-state copy, theme tokens, right-rail trends, places, topics, nests, Room Brief rail config (`rail`), taxonomy, profile copy (`skin`), feature flags (`features`, `dms`, `redditCrosspost`, `redditSr`), `minAge`, and a `seedNote` steward note. No sample seed posts, notifs or threads (removed in #19). Stories v0 is flagged here (`stories.enabled`); **only samochat** turns it on in this repo.
- `factory.js`: Auth email/password, live posts, image upload, poll, reply, delete, empty-state, feature-flagged Stories tray/viewer/create
- Rules: subx-factory [`rules/`](https://github.com/jebbdykstra99/subx-factory/tree/main/rules) is **not** a source of truth for what is live. Its `firestore.rules.proposed` is behind the live Firestore rules. Its `storage.rules.proposed` matches the live Storage rules as of 2026-10-22 (subx-factory #6). The live rules are whatever SubX last hand-published in the console. No rules files live in this repo. The shared nest index is in subx-factory [`factory/firestore.indexes.json`](https://github.com/jebbdykstra99/subx-factory/blob/main/factory/firestore.indexes.json).
- `firebase.indexes.json` (kept): this room's git copy of the live `posts` index (`siteId` + `createdAt`) and the Stories index (`stories`: `siteId` + `createdAt`). Each room keeps its own copy: bartchat has only the `posts` index, samochat adds a `stories` index, and gpchat and 27chat carry the same file (`posts` plus the `nestSlug` index). Keep it until those are recorded in subx-factory.
- SubX publishes rules and indexes by hand in the Firebase console for **subx-skins**. Do not `firebase deploy` from an agent.

## Product locks

- Guest is browse-only. Google provider stays off until enabled.
- Images `image/*` ≤ 5 MB. GIF is a user-uploaded `.gif`, not Tenor.
- AI off the hot path. No Reddit/X ingest.
- Preview banner and `robots.txt` noindex stay until SubX lifts them.
