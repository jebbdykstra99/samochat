# SubX factory.js clone audit

Read/report only. Audited 2026-09-17 from GitHub `jebbdykstra99` default-branch blobs. Workspace is **samochat**; other clones were read remotely (`gh api repos/jebbdykstra99/<repo>/contents/...`). No live-site rewrites. No cleanup PRs on other repos. bakasan.art is out of scope.

**Method:** download `factory.js`, `index.html`, `site.json`, `styles.css`, and `app.js` (when present) for all ten clones; compare SHA-256, line counts, function-name sets, `site.json` rail/stories flags, and remaining unified diffs between near-identical copies. Last `factory.js` commits pulled via the GitHub commits API.

**Constraint check:** every `index.html` still has the preview banner. No `gtag` / `GTM-` / `googletagmanager` / `dataLayer` script tags in any `index.html`. `factory.js` `sendPixel` (overlay trio only) posts to a first-party Cloud Function on `subx-skins`, not GTM.

---

## 1. There is no single golden shell

`factory.js` is **ten different files**. Zero SHA-256 matches.

| Cluster | Repos | Bytes | Lines | `factory.js?v=` | Last `factory.js` commit |
|---|---|---:|---:|---|---|
| Feature fork | **gpchat** | 183670 | 4765 | **38** | 2026-09-13 thumbs stamp |
| Overlay chrome | **gaichat** | 179314 | 4696 | **29** | **2026-09-16 overlay stamp** (newest commit) |
| Overlay chrome | **samochat** | 179127 | 4696 | **28** | 2026-09-13 thumbs |
| Overlay chrome | **415chat** | 178750 | 4690 | **28** | 2026-09-13 thumbs |
| Older chrome | bartchat, 808chat, buffettchat, 27chat | 170736–170762 | 4433 | 26–27 | 2026-09-13 thumbs |
| Older chrome (thinner stories helper) | popechat | 170600 | 4430 | 26 | 2026-09-13 thumbs |
| Older chrome (thinner stories helper) | recruitchat | 170346 | 4427 | 26 | 2026-09-13 thumbs |

What “newest” means depends on the question:

- **Newest commit:** gaichat (`4b5137e`, 2026-09-16) — ported overlay + outbound Room Brief. Not a full chrome rebase of the other nine.
- **Largest / most unique JS:** gpchat — F1 merge, topic-follow, session seeds, chat-verify drop, `dms: true`. **No** `railOverlayUiReady`.
- **Most complete shared chrome:** the overlay trio (415 / samo / gai). Same 24 overlay function names. Remaining diffs are ~11–18 unique lines (skin strings + two real algorithm hooks).
- **Best individual hooks (not a full file):**
  - 415chat: `syncRailOverlayViewport()` on right-rail collapse (`factory.js` L3587–3589).
  - samochat: `liveKeep` NWS+outbound merge (`factory.js` L2020–2024).
  - gaichat / bart / 808 / buffett / 27: parameterized `storiesComposePlaceholder()`.

Treating any one file as “the” stamp donor will overwrite the others. That is the P0 process risk.

---

## 2. Inventory (verified)

Known clue vs measured (clue held):

| Repo | factory.js bytes | `?v=` | `railOverlayUiReady` | `styles.css` rail-overlay rules | `site.json` `rail.kind` | stories.enabled | overlay.enabled |
|---|---:|---:|---|---|---|---|---|
| gpchat | 183670 | 38 | **absent** | 0 | `f1-calendar` | true | absent |
| 415chat | 178750 | 28 | 3 hits | 11 | `nws-forecast` | true | true (7000ms) |
| samochat | 179127 | 28 | 3 hits | 11 | `nws-forecast` | true | true (7000ms) |
| gaichat | 179314 | 29 | 3 hits | 11 | `porch` | true | true (8000ms) |
| bartchat | 170748 | 26 | absent | 0 | `bart-bsa` | true | absent |
| 808chat | 170743 | 27 | absent | 0 | `nws-forecast` | true | absent |
| buffettchat | 170762 | 26 | absent | 0 | `porch` | true | absent |
| 27chat | 170736 | 26 | absent | 0 | `porch` | true | absent |
| popechat | 170600 | 26 | absent | 0 | `porch` | true | absent |
| recruitchat | 170346 | 26 | absent | 0 | `porch` | true | absent |

GitHub blob SHAs for `factory.js` (all unique):

| Repo | blob SHA |
|---|---|
| 415chat | `8c5fd92290171704ac6734c4137481aaa70beea9` |
| samochat | `b83747f122711363d71f2365958b42b009c24d42` |
| gpchat | `5ac00abc24dd08674531d9b3dd51047abe9d3de5` |
| gaichat | `71adc1928331d499b81297248bc48c0e6fe88c78` |
| bartchat | `ff1ed6ea0656083a24d115b45b982131dfdfd65d` |
| 808chat | `36444983c916a686bf725358b59c9c3415d30378` |
| buffettchat | `aadf8016bc482ee3b1d2e62c8965de9b0b7f36d6` |
| 27chat | `53361e7372e89bc8a55f87bc3fd23d6d359dbe03` |
| popechat | `ae18c3b53aa3f177857ac044e3b2308fbe7143c8` |
| recruitchat | `b91dd9343c9c36abb90b972e2e4adc3f6068a853` |

Content SHA-256 (first 12 hex): 415 `3239c728b461` · samo `2c2f8005013f` · gp `f005b215434f` · gai `4df5d1a8fc3a` · bart `0738f478357f` · 808 `b941a646b55d` · buffett `d1e601df2962` · 27 `461e46c1b86d` · pope `2fe1220419ac` · recruit `2d2de45815bb`.

Porch dwell is on **all ten** (`porchDwellMs` ×3, `site.json` `rail.porch.dwellMs: 9000`). Stories tray thumbs (`storyTrayAvatarHtml`, `primeStoryTrayThumbs`) are on **all ten**. Stories is flagged on **all ten** (`site.stories.enabled: true`) — samochat `README.md` still says only samochat turns it on.

Unique function-name counts: 415/samo **275** · gai **276** · gp **273** · bart/808/buffett/27 **252** · pope/recruit **251**.

---

## 3. Feature matrix (what each copy actually carries)

| Feature | 415 | samo | gp | gai | bart | 808 | buffett | 27 | pope | recruit |
|---|---|---|---|---|---|---|---|---|---|---|
| Rail overlay JS + CSS + `site.json` | yes | yes | **no** | yes | no | no | no | no | no | no |
| Porch dwell + tally | yes | yes | yes | yes | yes | yes | yes | yes | yes* | yes |
| Stories v0 + tray thumbs | yes | yes | yes | yes | yes | yes | yes | yes | yes | yes |
| `storiesComposePlaceholder()` helper | no | no | no | yes | yes | yes | yes | yes | **no** | **no** |
| `storiesDebugOwnUid` localhost demo | no | yes | yes | yes | yes | yes | yes | yes | yes | **no** |
| Live NWS forecast (`nws-forecast`) | **yes** | **yes** | code only | code only | code only | **yes** | code only | code only | code only | code only |
| samo `liveKeep` merge | no | **yes** | no | no | no | no | no | no | no | no |
| F1 Jolpica/OpenF1 + `overlayOpenF1Cards` | code only | code only | **live** + `mergeF1Rail` | code only | code only | code only | code only | code only | code only | code only |
| BART BSA `fetchBartCards` | code only | code only | code only | code only | **live** | code only | code only | code only | code only | code only |
| NWS CWF `fetchCwfCards` | code only | code only | code only | code only | code only | code only | code only | code only | code only | code only |
| Chat email-verify drop (`action !== 'chat'`) | no | no | **yes** | no | no | no | no | no | no | no |
| `site.dms === true` | no | no | **yes** | no | no | no | no | no | no | no |
| Topic-follow + session seeds | no | no | **yes** | no | no | no | no | no | no | no |
| Dead `app.js` still in repo | yes | **no** | yes | yes | **no** | yes | yes | yes | yes | yes |

\*popechat porch tally HTML dropped `aria-live="polite"` (bart has it; pope does not).

`nws-cwf` does not appear in any `site.json`. CWF is dead weight in every copy.

---

## 4. Ranked findings

### P0 — Process: one more naive stamp will overwrite live chrome

Last ten `factory.js` commits are copy-stamps (“Stamp Stories tray media thumbs from samochat”, “Stamp factory.js from gpchat”, “Port Room Brief rail overlay from 415chat”). The copies have already diverged in ways a whole-file stamp cannot preserve.

**If the next stamp donor is gpchat or the older cluster → overlay trio:**

- 24 overlay functions exist only on 415 / samo / gai (`overlayCfg`, `wireRailOverlay`, `showRailOverlay`, `sendPixel`, `railOverlayUiReady`, …).
- Overlay CSS is only in those three `styles.css` files (11 `rail-overlay` selectors each; 0 elsewhere).
- gaichat’s 2026-09-16 overlay + 8 outbound cards would be the first thing a gpchat-shaped stamp would drop.

**If the next stamp donor is the overlay trio → gpchat:**

- gpchat-only functions (22): `mergeF1Rail`, `stalePreRaceCard`, `f1SessionUsable`, `f1PickResultSession`, `toggleTopicFollow`, `sessionSeedPosts`, `paintActiveChatName`, …
- Chat-verify drop at gpchat `factory.js` L402–404 (`action !== 'chat' && !isEmailVerified()`). The other nine still gate **all** actions including `'chat'` at the same helper (`if (!isEmailVerified())`).
- Only gpchat `site.json` has `"dms": true`. The verify drop is live there; it is latent everywhere else.

**Subtract-before-add rule for the next chrome pass (do not implement in this PR):** compose a donor from the overlay trio (415 collapse hook + samo `liveKeep` + parameterized stories placeholder). Stamp *into* gpchat. Never whole-file overwrite gpchat with an overlay clone, or an overlay clone with gpchat.

---

### P1 — Overlay hide is half-stamped on samochat and gaichat

`syncRailOverlayViewport()` dismisses the overlay when the right rail is not visible (`factory.js` ~L1174–1180 on all three overlay clones). **Only 415chat** calls it when the user toggles the rail (`415chat/factory.js` L3587–3589):

```javascript
document.getElementById('right-panel-tab').addEventListener('click', function () {
  document.body.classList.toggle('right-collapsed');
  syncRailOverlayViewport();
});
```

samochat L3591–3593 and gaichat L3587–3589 toggle `right-collapsed` and stop. Resize still syncs. Click-to-collapse does not. Overlay is enabled on both (`site.json` `rail.overlay.enabled: true`). This is a real stamp gap, not a skin difference.

---

### P1 — Room Brief merge algorithm exists on samochat only

samochat `renderTrends` keeps at least one live card when outbound extras fill the slot budget:

```2020:2024:/workspace/factory.js
      var slots = railNwsSlots();
      var liveKeep = Math.max(0, slots - extra.length);
      if (!liveKeep && (cards || []).length) liveKeep = 1;
      var merged = (cards || []).slice(0, liveKeep).concat(extra);
      if (merged.length) commitRail(merged);
```

415chat, gaichat, and the older cluster do `concat(extra)` then `slice(0, railNwsSlots())`. gpchat special-cases F1 through `mergeF1Rail`.

**Live today:** samochat has 1 outbound (NDBC 46221) + NWS, so `liveKeep` matters. 415 has 0 outbound, so the simpler merge is currently equivalent. gaichat is `kind: porch` with 8 outbound cards and never hits `liveFetch`. The drift is still a half-stamp: the later algorithm did not go back to 415.

---

### P1 — Cache-bust `?v=` is a per-repo integer, not a content hash

Last `factory.js` commit on every clone also touched `index.html`, so the *latest* stamp tried to bump `?v=`. Honesty problems that remain:

1. **Same `v=` ≠ same bytes.** 415chat and samochat both ship `factory.js?v=28` (178750 vs 179127 bytes, different SHA-256). bart / buffett / 27 / pope / recruit all ship `?v=26` with five different hashes.
2. **Counters are independent per asset.** recruitchat is `styles.css?v=19` + `factory.js?v=26`. gpchat is `factory.js?v=38` / `site.json?v=36` / `styles.css?v=14`. Operators comparing “who is on 28” will misread stamp state.
3. **No clone uses a content hash.** A silent factory edit that forgets to bump `index.html` will serve stale GitHub Pages JS. The integer cannot detect that.

This is not a cross-origin cache collision (each site is its own origin). It is stamp-bookkeeping drift.

| Repo | factory.js?v= | styles.css?v= | site.json?v= |
|---|---:|---:|---:|
| 415chat | 28 | 14 | 5 |
| samochat | 28 | 12 | 5 |
| gpchat | 38 | 14 | 36 |
| gaichat | 29 | 15 | 5 |
| bartchat | 26 | 13 | 2 |
| 808chat | 27 | 13 | 2 |
| buffettchat | 26 | 13 | 2 |
| 27chat | 26 | 13 | 2 |
| popechat | 26 | 13 | 3 |
| recruitchat | 26 | 19 | 2 |

---

### P1 — Chat-verify drop will not travel with a future DMs stamp

gpchat is the only room with live DMs (`site.json` `"dms": true`) and the only `requireVerified` that skips email verification for `action === 'chat'` (L402–404, comment: “Chat/DM replies: signed-in + not killed is enough”).

The other nine still have `chatErr` wiring for `action === 'chat'` but gate it behind `isEmailVerified()`. Harmless while `dmsOn()` is false (`dmsOn` is `site.dms === true`). If DMs are stamped factory-wide from an overlay/old clone, Google / unverified email users will be blocked from sending. If DMs are stamped from gpchat without the verify-drop comment/guard, same bug in reverse.

---

### P2 — Dead `app.js` in eight repos

`index.html` loads **only** `factory.js` on all ten clones (`<script src="factory.js?v=…">`). No `app.js` script tag.

| Repo | app.js | loaded? | last commit |
|---|---:|---|---|
| 415chat | 27410 B | no | 2026-08-20 Google-button copy |
| gpchat | 33845 B | no | 2026-08-20 |
| gaichat | 29150 B | no | 2026-08-20 |
| 808chat | 37682 B | no | 2026-08-20 |
| buffettchat | 30456 B | no | 2026-08-20 |
| 27chat | 37693 B | no | 2026-08-20 |
| popechat | 28945 B | no | 2026-08-20 |
| recruitchat | 28138 B | no | 2026-08-20 “Add app.js (dummy factory skin)” |
| samochat | absent | — | — |
| bartchat | absent | — | — |

Each leftover `app.js` is a pre-factory localStorage shell (`LS_USER = '<skin>.user'`, `initials` / `colorFor` / `escapeHtml` / hash router / dummy TRENDS). Duplicate helpers, not executed. Risk is an accidental `<script src="app.js">` fighting factory. Subtract: delete the eight files. Do not “fix” them.

---

### P2 — F1 + BART + CWF copied into every factory.js

Approximate live-path vs passenger code (overlay-trio line numbers; older cluster is the same blocks shifted ~260 lines earlier because overlay is missing):

| Block | Approx lines (415/samo/gai) | Live on |
|---|---|---|
| Overlay | L930–1188 (~260) | 415, samo, gai |
| NWS forecast `fetchNwsCards` | L1233–1358 | 415, samo, 808 |
| NWS CWF `fetchCwfCards` | L1359–1409 | **nobody** (`nws-cwf` unused) |
| BART `fetchBartCards` | L1410–1573 (~160) | bartchat |
| F1 Jolpica/OpenF1 `overlayOpenF1Cards` / `fetchF1Cards` | L1574–1843 (~270) | gpchat (plus gpchat-only `mergeF1Rail` L1847+) |
| Porch dwell + tally | ~L1858–2180 | all ten |
| Stories | ~L3777–4668 (~890) | all ten (flagged) |

`renderTrends` already switches on `rail.kind`. The passenger backends are not called unless `site.json` is pointed at them. Bloat is real: ~400–500 lines of F1+BART+CWF ride along on porch-only skins (buffett, 27, pope, recruit, gai). Name collision: `overlayOpenF1Cards` is an OpenF1 *data* overlay, not the Room Brief rail overlay.

Do not delete backends in a drive-by. Next stamp should not *re-grow* them. One shared path is the existing `rail.kind` switch — keep it, stop copying gpchat F1 patches onto porch skins.

---

### P2 — Porch CSS is defined twice

Every `factory.js` injects `#rail-porch-css` via `ensureRailCss()` (samochat L898–917) with `.porch-btn` / `.porch-tally` rules. Every `styles.css` also has a `.porch-btn` block (samochat ~L962, 415chat ~L243, recruitchat extra `.right-panel .porch-btn` rules). Same feature, two sources. Subtract the injector or the stylesheet copy; do not add a third.

---

### P2 — Stories composer placeholder implemented three ways

| Pattern | Repos | Evidence |
|---|---|---|
| Hardcoded skin string in the textarea | 415, samo, gp | samo: `placeholder="What is SAMO doing right now?"` · 415: `"What's happening in the 415?"` · gp: `"What is happening on the grid right now?"` |
| `storiesComposePlaceholder()` → `site.composePlaceholder` with a skin fallback | gai, bart, 808, buffett, 27 | gai fallback `"A tool with a receipt — not an oracle."` · bart `"What's happening on BART?"` |
| Inline `(site && site.composePlaceholder) \|\| "What's happening in this room?"` | pope, recruit | no helper function (pope/recruit are the 251-fn set) |

Same product, three stamps. The helper is the subtract-friendly path (skin lives in `site.json`).

---

### P2 — Localhost Stories debug and tray leftovers

- `storiesDebugOwnUid` + `__storiesDebug.setDemo(list, ownUid)` present on samo, gp, gai, bart, 808, buffett, 27, pope. **Stripped** on 415 and recruit (`setDemo(list)` only). Half-stamp.
- Localhost “Your story” avatar is still hardcoded on samo (`colorFor('samo')` + `SM`) and 415 (`colorFor('415')` + `41`). gai/old cluster derive initials from `SITE_ID`. pope/recruit use `initials(site.name)`.
- `window.__storiesDebug` and `window.subxKill` are exported on all ten. Used as operator hooks, not unused — but they are undocumented chrome.

---

### P2 — Leftover 415 defaults inside every factory.js

Every copy still opens with:

```javascript
const LS_USER = '415chat.user';
const LS_LIKES = '415chat.likes';
let SITE_ID = '415chat';
```

`SITE_ID` is reassigned from `site.json` on boot (`SITE_ID = site.siteId || SITE_ID`). `localStorage` is origin-scoped, so `415chat.user` as a key name does not leak across clones. It is overwrite residue from the original 415 stamp, not a cross-site bug. Same `ADMIN_UID` and the same `subx-skins` Firebase config appear in all ten (expected for this factory).

CWF defaults are the same class of residue: 415 / gai / older cluster default `productLocation` to `'PPG'` and `forecastPage` to `https://www.weather.gov/ppg/marine`. samochat defaults to `'LOX'` / `https://www.weather.gov/lox/`. No clone sets `rail.kind` to `nws-cwf`, so this is latent.

---

### P2 — Small a11y / docs drift

- popechat porch tally: `'<div class="porch-tally">'` vs bart `'<div class="porch-tally" aria-live="polite">'` (unified diff, first hunk).
- samochat `README.md` L19: “**only samochat** turns it on in this repo.” False for the fleet — all ten `site.json` files have `"stories": { "enabled": true }`. True only as a statement about *this* repo’s intent at write time.

---

## 5. Pairwise remaining diffs (near-identical copies)

Unique line-set sizes (not hunk counts):

| Pair | only-A | only-B | line-count delta | Character |
|---|---:|---:|---:|---|
| samo vs 415 | 18 | 12 | 6 | CWF default LOX vs PPG; **liveKeep vs concat**; 415 collapse sync; stories debug / hardcoded tray |
| samo vs gai | 11 | 11 | 0 | CWF default; liveKeep; gai `storiesComposePlaceholder`; SITE_ID tray initials |
| 415 vs gai | 8 | 14 | 6 | collapse sync vs stories helper / debug uid |
| samo vs gp | 236 | 311 | 69 | overlay (samo) vs F1/topic/seeds/verify-drop (gp) |
| samo vs bart (and 808/buffett/27) | 189 | 12 | 263 | overlay block (~260 lines) plus skin strings |
| bart vs 808 / buffett / 27 | 5 | 5 | 0 | placeholder + tray fallback strings only |
| bart vs pope | 8 | 6 | 3 | missing helper; aria-live; generic “Room” fallbacks |
| bart vs recruit | 14 | 8 | 6 | missing helper + stripped `storiesDebugOwnUid` |

Older cluster is one shell with skin strings. Overlay trio is one shell with two algorithm hooks that did not finish traveling. gpchat is a real fork.

---

## 6. Bloat score

| Metric | Overlay trio | Older cluster | gpchat |
|---|---|---|---|
| factory.js | ~179 KB / ~4696 lines / ~275 fns | ~170 KB / ~4433 lines / ~252 fns | 184 KB / 4765 lines / 273 fns |
| vs older cluster | +~8–9 KB ≈ overlay JS | baseline after Stories+thumbs | +~13 KB vs older; +~4.5 KB vs samo |
| Dead sibling | `app.js` 27–38 KB (except samo/bart) | same | 34 KB |
| Duplicated rail backends | F1+BART+CWF present | same | F1 live; BART+CWF passenger |
| Duplicated porch CSS | injector + stylesheet | same | same |

Line-count growth since the Sep 4 “Stamp factory.js from gpchat” wave is Stories (~890 lines) + overlay (~260) + porch dwell/tally (already in that wave) + thumbs (small). The expensive part is **copying every backend into every skin**, not Stories itself.

---

## 7. Recommended cleanup order (not this PR)

1. **Freeze a chrome donor in writing** before the next stamp. Suggested compose (subtract-before-add): overlay trio + 415 collapse hook + samo `liveKeep` + `storiesComposePlaceholder()` from gai/old cluster. gpchat stays a listed fork (F1 merge, topic follow, dms, verify drop).
2. **Stamp overlay onto 808** only if Room Brief overlay is now factory chrome (808 is already `nws-forecast` and has no overlay JS/CSS). Do not stamp overlay onto porch-only skins unless product asks.
3. **Delete the eight dead `app.js` files.** Confirm `index.html` has no reference first (already true).
4. **One stories placeholder path.** Prefer `site.composePlaceholder`. Remove hardcoded SAMO/415/grid strings and the pope/recruit inline duplicate.
5. **Stop shipping CWF defaults / F1 patches onto skins that do not set that `rail.kind`.** Keep the `kind` switch; do not grow passenger blocks.
6. **Cache-bust:** either content-hash (`factory.js?h=2c2f8005013f`) or a written rule that `v=` is per-repo and must bump on every factory/styles/site.json edit. Do not compare `v=28` across clones.
7. **Docs:** fix samochat README Stories sentence when someone next touches README. Not this PR.

---

## 8. What this audit is not

- Not a merge. Not a live-site fix.
- Not a claim that unused F1/BART/CWF functions currently fire (they do not, unless `rail.kind` is flipped).
- Not a GTM / preview-banner regression. Preview banner is on all ten `index.html` files. No GTM tags found.
- Not bakasan.art / a second Firebase. All ten `factory.js` files initialize **subx-skins**.

---

## 9. Sources

Remote reads: `GET /repos/jebbdykstra99/{repo}/contents/{factory.js,index.html,site.json,styles.css,app.js}` and `GET /repos/jebbdykstra99/{repo}/commits?path=factory.js`. Local confirmation: `/workspace/factory.js` matches the samochat remote blob (179127 bytes, `?v=28`).
