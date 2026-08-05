# Crawl test site

Nine static pages (`index`, `about`, `services`, `team`, `gallery`, `blog`,
`faq`, `contact`, `edge-cases`), each cross-linked via a top-level `<nav>` and
each containing one easy-to-edit line:

```html
<p id="content-marker"><strong>Content marker:</strong> home-v2</p>
```

Pages also include free-to-use photos from [Lorem Picsum](https://picsum.photos/)
(no attribution required), stored locally under `images/` with a deterministic
`?seed=` per page so crawls stay stable across runs unless the fixture is
intentionally changed. Shared layout/styling lives in `style.css`.

Note: navigation deliberately lives in its own `<nav>`, *not* inside
`<header>`/`<footer>`/`<aside>`/`<form>` — those tags are excluded by default
(`CrawlParams.excluded_tags`), and crawl4ai removes them from the page's DOM
*before* it looks for links to follow. Put navigation inside one of those tags
and the crawler finds zero outgoing links, no matter what `max_depth` is set to
— that was the actual cause of only the home page getting crawled with the
first version of this fixture.

## Link-resolution edge cases

`edge-cases.html` covers eight cases a link/URL resolver has to get right:
root-relative (`/x`), path-relative (`x`), protocol-relative (`//x`),
fragment-only (`#x`), `mailto:`/`tel:`, a plain absolute URL, a `<base href>`
page (`base-tag-test.html`), and a relative link buried in a non-standard
custom element. It links out to three small standalone target pages
(`sibling.html`, `other-page.html`, `base-tag-test.html`) that only exist to
be resolved to, so they're deliberately left out of `sitemap.xml`.

Two of these are expected to *not* resolve to a real page once deployed:
this repo is a GitHub Pages **project** site (served under
`/test-crawl-website/`, not the domain root), so `/other-page.html` and the
`<base href="/some/other/path/">` link in `base-tag-test.html` both resolve
outside the repo subpath. That mismatch — and whether a crawler notices it —
is the point of those two cases, not a bug to fix.

## Publish

1. Create a new GitHub repo, push this folder as its contents.
2. Enable GitHub Pages for it (Settings → Pages → deploy from the default branch, root).
3. Use the resulting `https://<user>.github.io/<repo>/` URL as `start_url` when
   creating a crawl source in the Crawler section.

## Testing change detection

1. Create the crawl source, run it once — the 9 nav-linked pages should show
   up in the report as `new` (plus `sibling.html` and `base-tag-test.html` if
   the crawler follows the links from `edge-cases.html`; see above for why
   `other-page.html` is a special case).
2. Run it again unchanged — all of those should now show as `unchanged`, and
   the Status column's page count shouldn't grow.
3. Edit one page's content-marker line (e.g. bump `about-v5` → `about-v6`),
   commit and push (GitHub Pages redeploys automatically within ~a minute).
4. Run the crawl source again — only that one page should show as `updated`
   in the report; the rest should stay `unchanged`.
