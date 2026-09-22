# Shifteryadak CSV Price-Sync Plugins (Client Fix Drop)

Three WooCommerce plugins plus the two Python scrapers that feed them, saved as the fix set delivered
for the Persian parts store `shifteryadak.ir`. The plugins push scraped wholesale prices from
`cookmamotor.com` and four competing shops into WooCommerce products at a per-product markup
percentage. This is the May 2026 revision; later copies of the same plugins exist elsewhere.

**Suggested repo name:** `csv-price-sync-client-fix`
**Stack:** PHP 7.4+/8.x WordPress plugins (WooCommerce, `wp_cron`, `wp_post_meta`), Python 3 with Playwright, requests and BeautifulSoup
**Status:** archived
**Last modified:** 2026-05-18

## What it does

- `cookma-woo/cookma-woo/cookma-woo.php` - "WooCommerce CSV Price Sync" v4.0. Finds
  `cookma_YYYY-MM-DD.csv` in `ABSPATH` (today, walking back 30 days in `Asia/Tehran`), keys rows by
  `شناسه (Hash ID)`, and rewrites product prices from base price × `_csv_markup_percent`. Meta used:
  `_csv_hash_id`, `_csv_markup_percent`, `_csv_matched_name`. Registers a custom `every_5_hours`
  cron interval, an admin page, an AJAX single-product "apply price" action, and a log file.
- `check-new-product/check-new-product/check-new-product.php` - "CSV Upload & Compare for
  WooCommerce". Upload a new CSV, diff it against a reference CSV published at
  `https://shifteryadak.ir/wp-content/uploads/cookma_2026-05-18.csv` (and the `other_sites_` twin),
  show added/changed/removed rows in a tabbed admin screen, then apply once.
- `others/others/others.php` - same price-sync engine for the four other shops
  (مسترکلاه، استارسیکلت، کلاه‌کاسکت، گازرو), matching by `لینک محصول` from
  `other_sites_YYYY-MM-DD.csv`; marks products out of stock when their link disappears.
- `final-files1/Final Files/price.py` - Playwright crawl of the cookma product list →
  `~/Desktop/cookma/cookma_YYYY-MM-DD.csv` with `عنوان محصول, دسته‌بندی, ویژگی‌ها, قیمت (تومان),
  شناسه (Hash ID), لینک محصول`; converts rial to toman.
- `final-files1/Final Files/other_sites_scraper.py` - static crawl of `mrkasket.com` and
  `starcyclet.com` → `~/Desktop/other_sites/other_sites_YYYY-MM-DD.csv`.

## Layout

```
check-new-product/check-new-product/check-new-product.php
cookma-woo/cookma-woo/cookma-woo.php
others/others/others.php
final-files1/Final Files/price.py
final-files1/Final Files/other_sites_scraper.py
```

## Running it

The plugins are dropped into the store as single files, in a directory of the same name under
`wp-content/plugins/` (this is exactly what the sibling deploy scripts in
`Documents/New Coding project/shifter_work/do_ftp_upload.py` do).

The scrapers run locally on Windows with Playwright installed:

```bash
python "final-files1/Final Files/price.py"
python "final-files1/Final Files/other_sites_scraper.py"
```

## Notes

- Every plugin sits two levels deep (`cookma-woo/cookma-woo/cookma-woo.php`) because the delivery
  zips were extracted over themselves; only the innermost file is the real plugin.
- This is WordPress/WooCommerce code, not OpenCart, despite the folder being grouped with OpenCart
  feed work elsewhere.
- `price.py:12` hardcodes a live cookma `PHPSESSID` session value as a literal - that is a credential
  in source. Move it to an environment variable before publishing.
- Superseded by `Documents\Projects\shiftery-fix` (adds a PHP port of the scrapers) and
  `Documents\Projects\shiftery-plugins` (newer plugin revisions with the bulk-markup screens).
  Content-wise the four files here are byte-identical to the matching files in `shiftery-fix`.
- Publishing means exposing a client store's pricing automation and its live shop URLs. Scrub the
  reference CSV URLs and session constant first, or keep it private.
