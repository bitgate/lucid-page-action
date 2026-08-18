# KEVIN.md

Scratchpad for Kevin. Not public-facing docs — see README.md for those.

## What this is

GitHub Action publishing Markdown to lucid.page (`POST /publish`, or `POST /<slug>` with Bearer for owner updates). Composite, pure bash + curl + jq (both preinstalled on GitHub runners). Sibling: bitgate/ship-page-action (same shape, brash brand, chunking — lucid needs none, 1 MB single-request cap).

## State

- v1.0.0 + v1 annotated tags on the scaffold commit. Marketplace listing: Bart does manually.
- CI (`test.yml`) live-publishes `sample/CHANGELOG.md` to prod lucid.page anonymously on every push and greps the rendered page for "lucid.page action sample". Two publishes per run — well under the 10/min/IP anonymous cap.

## Gotchas

- lucid.page update endpoint ignores `visibility`/`ttl` in the body — only markdown + title are applied. Action still sends visibility (harmless).
- Update response has no `expires_at`; the step-summary Expires row only appears on creates with ttl.
- Anonymous docs on lucid.page do NOT expire by default (unlike ship.page's 7 days) — `expires_at` is null unless ttl is set.
- Branding: icon `feather`, color `purple` (GitHub's allowed set is limited; fits the literary vibe).
