# Crawl test site

Five static pages (`index`, `about`, `services`, `team`, `contact`), each
cross-linked via `<nav>` and each containing one easy-to-edit line:

```html
<p id="content-marker"><strong>Content marker:</strong> home-v1</p>
```

## Publish

1. Create a new GitHub repo, push this folder as its contents.
2. Enable GitHub Pages for it (Settings → Pages → deploy from the default branch, root).
3. Use the resulting `https://<user>.github.io/<repo>/` URL as `start_url` when
   creating a crawl source in the Crawler section.

## Testing change detection

1. Create the crawl source, run it once — all 5 pages should show up in the
   report as `new`.
2. Run it again unchanged — all 5 should now show as `unchanged`, and the
   Status column's page count shouldn't grow.
3. Edit one page's content-marker line (e.g. bump `about-v1` → `about-v2`),
   commit and push (GitHub Pages redeploys automatically within ~a minute).
4. Run the crawl source again — only that one page should show as `updated`
   in the report; the other four should stay `unchanged`.
