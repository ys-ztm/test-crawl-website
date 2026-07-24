# Crawl test site

Eight static pages (`index`, `about`, `services`, `team`, `gallery`, `blog`,
`faq`, `contact`), each cross-linked via a top-level `<nav>` and each
containing one easy-to-edit line:

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

## Publish

1. Create a new GitHub repo, push this folder as its contents.
2. Enable GitHub Pages for it (Settings → Pages → deploy from the default branch, root).
3. Use the resulting `https://<user>.github.io/<repo>/` URL as `start_url` when
   creating a crawl source in the Crawler section.

## Testing change detection

1. Create the crawl source, run it once — all 8 pages should show up in the
   report as `new`.
2. Run it again unchanged — all 8 should now show as `unchanged`, and the
   Status column's page count shouldn't grow.
3. Edit one page's content-marker line (e.g. bump `about-v4` → `about-v5`),
   commit and push (GitHub Pages redeploys automatically within ~a minute).
4. Run the crawl source again — only that one page should show as `updated`
   in the report; the other seven should stay `unchanged`.
