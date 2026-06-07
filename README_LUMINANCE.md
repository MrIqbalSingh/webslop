# Luminance — consolidated release (v1.0.0)

A hand-built WordPress photography site for a wedding/event photographer. This
single bundle merges all five redesign phases into one clean release:

- **Theme:** `luminance-studio` (presentation) — v1.0.0
- **Plugin:** `luminance-core` (functionality) — v4.0.0

The architecture principle throughout: **functionality lives in the plugin,
presentation in the theme**, so the site survives a theme switch and the two can
be maintained independently.

---

## What's included (all phases)

**Phase 1 — Foundation:** standards-compliant companion plugin (WPCS naming,
ABSPATH guards, translation-ready), Portfolio custom post type + collection
taxonomy, content seeding, menu management.

**Phase 2 — Performance:** non-blocking fonts, LCP image preload (hero + first
gallery thumbnail), deferred JS, responsive `srcset`/`sizes`, optimised featured
query, head cleanup, dequeued core block/global CSS. Natural-height masonry on
the home page.

**Phase 3 — Security:** verified input sanitisation, output escaping, prepared
SQL, capability-gated actions; added version hiding, XML-RPC disabled, REST +
`?author=N` enumeration blocked for anonymous visitors, security headers,
`DISALLOW_FILE_EDIT`.

**Phase 4 — Scalability:** class autoloader, archive pagination + query tuning
(by `menu_order`), transient-cached featured query with auto-invalidation on
save, extension filters (`luminance_portfolio_per_page`,
`luminance_journal_per_page`, `luminance_featured_count`).

**Phase 5 — Accessibility & Responsiveness:** keyboard focus rings
(`:focus-visible`), visible skip link, lightbox as an accessible modal (focus
trap + restore), `aria-current` nav, 44px touch targets, full responsive range
(mobile -> ultrawide), WCAG AA colour contrast, meta description. Balanced
uniform grid on the portfolio archive. Minified CSS/JS served automatically.

**Lighthouse (production-representative):** Desktop 100/100/100/100; Mobile
~97-100/100/100/100 (remaining mobile performance depends on real WebP images +
host compression, not theme code).

---

## Installation (fresh site)

1. Copy into your WordPress install:
   - `wp-content/plugins/luminance-core/`
   - `wp-content/themes/luminance-studio/`
2. In **wp-admin → Plugins**, activate **Luminance Core**. (Activation
   registers the Portfolio post type and flushes rewrite rules.)
3. In **wp-admin → Appearance → Themes**, activate **Luminance Studio**.
4. On activation the plugin seeds collections, demo galleries, pages and menus
   if they don't already exist. It is safe to re-run (skips anything present).
5. **wp-admin → Settings → Permalinks → Save Changes** (no need to change
   anything) — guarantees the `/portfolio/` archive and gallery URLs resolve.

### Local (Local by Flywheel) deploy notes
These two steps resolve the most common "nothing changed / page is blank"
symptoms during development:
- **After changing PHP files:** a plain Stop→Start may not clear PHP's opcache.
  Toggle the site's **PHP version** in Local and back — this fully recycles
  PHP-FPM and clears the opcache. Then hard refresh.
- **After changing CSS/JS:** a hard refresh (Ctrl/Cmd+Shift+R) is enough.
- **If a custom-post-type URL 404s on a fresh clone:** Settings → Permalinks →
  Save Changes (flushes rewrite rules).

---

## Customisation

**Gallery order:** each gallery has an **Order** value (Portfolio → Quick Edit →
Order). Lower numbers appear first, on both the home page and the archive. Number
them 1, 2, 3… for a deliberate sequence; galleries left at 0 sort first.

**Per-page counts / featured count** (via a child theme or small plugin):
```php
add_filter( 'luminance_portfolio_per_page', fn() => 9 );
add_filter( 'luminance_journal_per_page',   fn() => 6 );
add_filter( 'luminance_featured_count',     fn() => 8 );
```

**Real images & best performance:** export photos as **WebP** (~q80-82), sized
to display, and upload via the media library. The theme's `srcset`/`sizes` will
serve the right size/format. The home page's "Selected work" uses natural-height
masonry; the portfolio archive uses a balanced uniform 4:5 grid.

**Editing CSS/JS:** edit the readable `assets/css/main.css` / `assets/js/main.js`,
then regenerate the `.min` files — or simply delete the `.min` files and the
theme falls back to the readable source automatically. Don't hand-edit the
minified files.

---

## Operational security (do this on the real site)
- Change the default `admin` / `password` login to a strong, unique password —
  this is the single biggest real-world risk and isn't something theme code can
  protect against.
- Keep WordPress core and plugins updated.

---

## File map
```
wp-content/
  plugins/luminance-core/        # functionality (v4.0.0)
    luminance-core.php           # bootstrap + autoloader registration
    includes/
      class-luminance-autoloader.php
      class-luminance-core.php   # orchestrator + activation lifecycle
      post-types/                # Portfolio CPT + seeder
      content/                   # menus, contact form, scalability
      admin/                     # maintenance, security
  themes/luminance-studio/       # presentation (v1.0.0)
    *.php                        # templates
    assets/css/main.css(.min)
    assets/js/main.js(.min)
    style.css
```
