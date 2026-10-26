# My Shows contributor guide

## Project layout

- `myshows/index.html` is the shipped TV-episode tracker. It is a self-contained vanilla HTML/CSS/JavaScript app served at `/myshows/`.
- Root `index.html` is the public marketing/landing page. It describes **My Shows v2** (app at `https://v2.myshows.fyi/`, repo `CyberAdvisor/MyShows-V2`) as the default, and links to v1 (`/myshows/`) and the v1 guide for legacy users. Its screenshots are `images/v2-list-dark.png` and `images/v2-detail-dark.png` (dark only); `images/app-preview-dark.png` is the old v1 preview, kept but no longer used by the landing page.
- `guide/index.html` and `changelog/index.html` are the v1 guide and changelog. The landing page's guide and changelog links point to the v2 versions at `https://v2.myshows.fyi/guide/` and `/changelog/`.
- `logic.js` is the CommonJS, testable copy of the tracker’s pure logic. Its corresponding functions are intentionally duplicated in the inline script in `myshows/index.html`; keep both implementations in sync.
- `logic.test.js` contains unit tests; `app.test.js` is a jsdom integration suite. The copies under `myshows/` are the ones that exercise the tracker, because they resolve `myshows/index.html` relative to themselves.

## Development rules

- Keep the product dependency-free: do not introduce a framework, build step, or backend unless the requested work explicitly requires it.
- Preserve the mobile-first, simple, readable UI. The app is expected to work as a static HTTPS site, particularly on iPhone Safari.
- Store data only in browser `localStorage` under `my-shows-library-v1`; do not replace it with a platform-specific storage API.
- Treat `watchedEpisodeId` as the persistent progress identifier. Do not persist season/episode numbers as a replacement: multiple TVmaze specials can share a season with no episode number.
- Fetch episodes with `/shows/:id/episodes?specials=1`. The `?embed=episodes` shortcut omits specials. Preserve chronological `airdate` ordering, including specials.
- Keep the add-show search input stable while results update; replacing that input while typing loses focus on iOS Safari.
- Preserve archive behavior as an override layered on computed show status. An archived item keeps a frozen archive snapshot and must resume its real status when reactivated.
- Do not add a `<script>` tag after the tracker’s main inline script. `myshows/app.test.js` extracts that script using a greedy regex; analytics or other script tags belong in `<head>` before it.

## Verification

Run tracker tests from the repository root:

```sh
node myshows/logic.test.js
node myshows/app.test.js
```

`app.test.js` needs Node.js and `jsdom`. Add or update tests for behavior changes, especially changes to episode ordering, progress, local storage, search interactions, backup/restore, and archiving.

## Documentation and deployment

- Update the relevant public page (`README.md`, `guide/index.html`, or `changelog/index.html`) when a user-visible workflow changes.
- GitHub Pages serves this repository as a static HTTPS site; `CNAME` defines the custom domain. Avoid changes that assume server-side routing or APIs.
- Keep user privacy statements accurate. For the v1 tracker: the watch list stays on-device; external calls are for TVmaze data and the documented analytics beacon only. The landing page describes v2, which also syncs the list to a secret gist in the user's own GitHub account. The landing page loads the same Statcounter snippet (project 13340854, invisible) in its `<head>`, and its privacy paragraph discloses it.

For fuller behavioral history and edge cases, read `myshows/CLAUDE_CONTEXT.md` before making tracker changes.
