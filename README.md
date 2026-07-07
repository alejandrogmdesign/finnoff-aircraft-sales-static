# Finnoff Aviation Sales — Static Site

A static export of the Finnoff Aviation Sales site (Pilatus PC-12 aircraft-for-sale
listings; WordPress + Elementor Pro), repaired, slimmed, and made fully relative for
**portfolio hosting on Cloudflare Pages or GitHub Pages**.

> All internal links and asset references use **relative paths**, so the site works
> from a project subpath (`username.github.io/repo/`), a user site, a custom domain,
> or the local filesystem.

## Deploying to Cloudflare Pages

The repo is pre-optimized for Pages and well within its limits (3,880 files < 20,000;
largest file 4 MB < 25 MiB).

**Git integration** (recommended): Cloudflare dashboard → *Workers & Pages → Create →
Pages → Connect to Git*, pick this repo, leave **Build command empty** and **Build
output directory = `/`**. Every push redeploys.

**Direct upload** (no repo needed):

```bash
npx wrangler pages deploy . --project-name finnoff-aircraft-sales
```

What Pages picks up automatically from this repo:

- **`_headers`** — security headers site-wide; 1-year `immutable` caching for the frozen
  `/wp-content` + `/wp-includes` asset trees (HTML pages revalidate on every visit, so
  redeploys show up immediately). ⚠️ If you hand-edit a CSS/JS file **in place**, bump its
  `?ver=` string in the referencing HTML or rename the file — browsers won't refetch an
  immutable URL for a year.
- **`_redirects`** — two listing pages (`/archive/page/4/`, `/tag/v1/page/7/`) were never
  captured in the original export; their pagination links redirect back to the start of
  the listing instead of dead-ending.
- **`404.html`** — served for any unknown path (self-contained, branded).

Notes: `wrangler pages deploy` ignores `.gitignore`/`.assetsignore`, so keep the tree
free of `.DS_Store` before an upload (`find . -name .DS_Store -delete`); macOS Finder
recreates them when browsing folders. `robots.txt` has no `Sitemap:` line and the
sitemap `<loc>` values are relative — add absolute URLs only if search indexing of the
deployed copy ever matters (for a portfolio copy it usually shouldn't).

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
- **Fixed gallery lightbox links (Cloudflare Pages pass)** — 64 Elementor lightbox `href`s
  on 12 aircraft-listing pages still pointed at the `finnoff-aviation-aircraft-sales.local`
  dev domain (full-size gallery photos failed to open); rewritten to relative
  `../wp-content/uploads/...` paths, all 64 targets verified present.
- **Fixed footer/parent-site links (Cloudflare Pages pass)** — the footer "Finnoff Aviation"
  link on all 112 pages pointed at the abandoned `finnoff.a2hosted.com` staging clone (now
  `https://finnoff.com/`), and 77 insecure `http://finnoff.com` anchors were upgraded to
  `https`.

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
