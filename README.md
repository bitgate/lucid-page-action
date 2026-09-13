# lucid.page action

Publish Markdown to [lucid.page](https://lucid.page) from CI — release notes, docs, and RFCs, typeset beautifully and returned as a shareable URL. No account required for anonymous publishing.

Lucid also provides a signed-in Personal + Projects wiki. This Action is the retained standalone publishing integration, not a project-management, invitation or wiki-import client. Creating a page here does not place it in a project.

## Usage

```yaml
- uses: bitgate/lucid-page-action@v1
  id: publish
  with:
    file: CHANGELOG.md
- run: echo "Published at ${{ steps.publish.outputs.url }}"
```

That's the whole ritual. One file in, one elegant page out.

## Anonymous vs. API key

The snippet above needs **no account** — anonymous publishing is the default:

- No signup, no secrets. Just `file:`.
- ~10 publishes/min per IP, documents up to 1 MB.

Want higher limits, private documents, or the ability to update a page in place? **[Create an account →](https://lucid.page/dashboard)**, mint an API key, and pass it as `api-key` (or set the `LUCID_API_KEY` env var once at workflow level):

- Active subscriptions remove the additional free-account IP limit; the account limit is 120/min, subject to shared service admission limits.
- `visibility: private` — owner-only pages, gated and uncached.
- `slug:` — update a document you own, keeping its URL.

## Example: release notes on every release

```yaml
on:
  release:
    types: [published]

steps:
  - uses: actions/checkout@v4
  - uses: bitgate/lucid-page-action@v1
    with:
      file: CHANGELOG.md
      title: Release ${{ github.event.release.tag_name }}
      visibility: public
```

## Example: docs on every push

```yaml
on:
  push:
    branches: [master]
    paths: ['docs/**']

steps:
  - uses: actions/checkout@v4
  - uses: bitgate/lucid-page-action@v1
    with:
      file: docs/OVERVIEW.md
```

## Example: a living document

Publish once with an API key, keep the slug, and every subsequent run updates the same page — one permanent URL, always current:

```yaml
- uses: bitgate/lucid-page-action@v1
  with:
    file: ROADMAP.md
    slug: ${{ vars.LUCID_ROADMAP_SLUG }}
    api-key: ${{ secrets.LUCID_API_KEY }}
```

Stash the slug from the first run's `slug` output in a repo variable. This Action supports updating your standalone pages. If you move the page into a project, its updates require `expected_version`, which this Action does not send; use the workspace editor instead. An API key is not a project collaboration feature.

## Inputs

| Input | Required | Description |
|---|---|---|
| `file` | yes | Path to the Markdown file to publish |
| `title` | no | Document title. Default: derived from the Markdown |
| `visibility` | no | `unlisted` (default), `public` (indexable), or `private` (owner-only — requires `api-key`) |
| `ttl` | no | Document lifetime in seconds (min 60). Default: no expiry |
| `slug` | no | Slug of an existing doc to update in place. Requires `api-key` |
| `api-key` | no | lucid.page API key (`lp_...`) — pass via `secrets`. Falls back to a `LUCID_API_KEY` env var if unset |

## Outputs

| Output | Description |
|---|---|
| `url` | URL of the published page |
| `slug` | Document slug — save it to update the page later |

## Notes

- Documents never expire unless `ttl` is set — ephemeral or evergreen, your call.
- Anonymous and free-account publishes retain the ~10/min IP limit. Authenticated accounts also have a 120/min account limit; shared admission budgets may reject earlier. An API key alone does not remove free-plan limits. On 429 the action retries 3× with increasing backoff before failing.
- The publish body is capped at 1 MB.
- Every run writes the URL, slug, and visibility to the job summary automatically.
- `LUCID_PAGE_BASE_URL` overrides the endpoint (staging/self-hosted). Default: `https://lucid.page`.

## License

[MIT](LICENSE)
