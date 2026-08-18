# My Shows contributor guide

## Project layout

- `myshows/index.html` is the shipped TV-episode tracker. It is a self-contained vanilla HTML/CSS/JavaScript app served at `/myshows/`.
- Root `index.html` is the public marketing/landing page. `guide/index.html` and `changelog/index.html` are separate static pages.
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
- Keep user privacy statements accurate: the watch list stays on-device; external calls are for TVmaze data and the documented analytics beacon only.

For fuller behavioral history and edge cases, read `myshows/CLAUDE_CONTEXT.md` before making tracker changes.
