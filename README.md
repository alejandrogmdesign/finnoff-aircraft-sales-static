# Finnoff Aviation Sales — Static Site

A static export of the Finnoff Aviation Sales site (Pilatus PC-12 aircraft-for-sale
listings; WordPress + Elementor Pro), repaired, slimmed, and made fully relative for
**portfolio hosting on GitHub Pages**.

> All internal links and asset references use **relative paths**, so the site works
> from a project subpath (`username.github.io/repo/`), a user site, a custom domain,
> or the local filesystem.

## Deploying to GitHub Pages

1. Push this directory to a GitHub repository.
2. **Settings → Pages → Build and deployment** → Source = *Deploy from a branch*, pick
   `main` / `/ (root)`, save.
3. Published at `https://<username>.github.io/<repo>/`. No `CNAME` or custom domain needed.
4. `.nojekyll` makes Pages serve files as-is (no Jekyll processing).

## What was fixed after the Simply Static export

- **Restored missing Elementor Pro assets** — `frontend.min.css/js`, `webpack-pro.runtime.min.js`,
  `elements-handlers.min.js`, all `widget-*.min.css`, plus 45 lazy-loaded `*.bundle.min.js`
  webpack chunks (these prevent `ChunkLoadError`s).
- **Restored the `ele-custom-skin` plugin assets and the `e2pdf` frontend assets** that the
  export missed (CSS/JS were referenced but absent).
- **Made the site self-contained** — converted internal `aircraft.finnoff.com` links, all
  root-relative `/wp-content` asset paths, `data-settings` background images, and 225 CSS
  `url()` font/image references to **relative paths**. External links (the parent
  `finnoff.com` site, Google Fonts, social) were left absolute.
- **Removed leftover staging URLs** — one listing page (`2006-pc-12-47-3`) had been exported
  with absolute `finnoffaircstg.wpenginepowered.com` URLs (it even loaded its CSS from
  staging); all were rewritten to relative, and the WordPress oembed/RSD discovery `<link>`
  tags (which carried the staging domain) were stripped site-wide.

## Slimmed the repo: ~1.5 GB → ~190 MB

Removed only verified-unreferenced content:
- the 671 MB Simply Static backup archive, the Elementor template-kit demo library (42 MB),
  and the unused e2pdf upload cache (15 MB);
- the export/backup and Yoast SEO plugins (unused on the frontend);
- ~280 non-minified dev-duplicate scripts;
- ~4,760 unreferenced WordPress image variants (`.bk.jpg`, `.jpg.webp`, unused sizes) — 528 MB.

Every removed file was confirmed to have zero references in the rendered pages.

## Known / out-of-scope notes

- This is a **static** mirror — forms (contact/inquiry) and the "Spec Sheet" PDF generator
  won't submit/generate without a backend. The pages render exactly like the live site.
- `aircraft.finnoff.com` (the original production domain) was not resolving at export time, so
  assets were pulled from the live staging site; Open Graph / canonical tags remain absolute to
  the original domain (harmless metadata, not fetched for rendering).
- One icon CSS file (`ele-custom-skin .../ecs-mobile-menu.css`) returns 403 on the source
  server, so it isn't loadable on the live site either — its absence matches live behavior and
  affects only mobile-menu styling.

## Previewing locally

```bash
python3 -m http.server 8000
# then open http://localhost:8000/
```
