# Firebase rules (source of truth in this repo)

These files are **not** published by GitHub Pages and must **not** be deployed from this agent.

Project: **subx-skins** (not bakasan-art).

Publish by hand in the Firebase console:

1. Firestore rules from `firestore.rules` (includes `match /stories/{id}` — deploy to **subx-skins**, do not assume Pages agents can `firebase deploy`)
2. Storage rules from `storage.rules` (includes `stories/{siteId}/{uid}/{file}` — image/* or video/mp4|webm, 8 MB)
3. Composite indexes from `firebase.indexes.json`
   - `posts`: `siteId` ASC, `createdAt` DESC
   - `stories`: `siteId` ASC, `createdAt` DESC (optional; the client filters TTL in-memory and does not require this index to list)

Until the posts index exists, the live feed query will fail in the compose error line — that is expected, not a fake-success path.

Stories are 24h TTL (`createdAt` + ~24h `expiresAt`). No permanent blobs intended. Reports may include `storyId` instead of `postId`.
