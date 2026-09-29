# PolishedPD — Technical Issues Fix Handoff

**Site:** polishedpd.com (Webflow Site ID: 67bf5f35cea87b89abb74e35, "DINAH | Polished Pediatric Dentistry")
**Date:** 2026-09-29
**Scope:** Asana ticket 1218698168907453 — 8 technical issues from the dev checklist
**Source:** Live sitemap crawl (89 URLs; no Screaming Frog export was available) + Webflow MCP element reads
**Publishing rule:** Staging only (`wond-dinah.webflow.io`). Custom domains were NOT published. Live `polishedpd.com` is unchanged.

## Summary
Audited all 89 sitemap URLs. Most findings sit in shared Components (Navbar, Footer, Blog Related), which the Webflow API cannot edit. Applied the page/template-level heading fixes and image compression that the API can do, published to staging only, and verified `/contact` on staging. Remaining items are listed under Manual and Needs decision.

## Issues found, by bucket
| Bucket | Found | MCP fixable | Fixed | Manual |
|---|---|---|---|---|
| Images: missing alt | 48 pages (blog-card images in "CMS Section / Blog / Related" Component render `alt=""`; 3 cards on 45 pages) | No (Component) | 0 | Yes |
| Images: missing size attributes | 89/89 pages | Partly | 0 | Yes |
| Images: over 100 kB | 39 images | Yes | 7 compressed (8.5 MB -> 3.9 MB); 3 JPEGs grew as WebP (skipped); AVIF batch failed | Retry |
| Security: unsafe cross-origin links | 87 pages (Navbar/Footer Component links) | No | 0 | Yes |
| H2: duplicate | 0 pages found | n/a | n/a | Check SF export |
| H2: non-sequential | 80 pages | Partly | See below | Yes for Component headings |
| 3xx internal | 0 found | n/a | n/a | Check SF export |
| 4xx external | cdc.gov/oralhealth returns 403 to bots (likely bot block) | n/a | n/a | Manual review |

## Fixes applied — full detail
Method for headings: `data_element_tool` `set_heading_level` (+ `set_style` to keep the previous look, because Webflow tag styles differ: e.g. h4 = 2rem/500, h3 = 2.5rem/400).

| Page/template | Change | Verified |
|---|---|---|
| Contact (`/contact`) | Email/Phone/Office h4 -> h3, class `heading-style-h4` added to keep size | Staging: h3 confirmed. Styling not visually compared (browser could not trust proxy CA). Note: `heading-style-h4` has weight 400 vs tag h4 500 |
| Blog Posts template | "Share" h5 -> h3, class `heading-style-h5` | Staging: `h3->h5 'Share'` skip gone (live still has it) |
| Services template | FAQ subtitle h6 -> h3, classes `heading-style-h6` + `text-color-secondary` | Staging: `h2->h6` skip gone on /services/composite-fillings |
| Service Categories template | FAQ subtitle h6 -> h3 (same classes) | Staging: `h2->h6` skip gone |
| Freehold, Old Bridge, Holmdel location templates | FAQ subtitle h6 -> h3 (same classes) | Staging: `h2->h6` skip gone on one item page each |

Side effect seen on staging: with the subtitle now h3, the FAQ question cards (h5, inside the "FAQ card"/Accordion Component) show as `h3->h5`. Net skips per page is unchanged (one), but moving to a clean structure needs the Component heading changed h5 -> h4 in Designer.

Reverted: Services template "Still have questions? Give us a call!" h5 was set to h3 but the three-class combo (`heading-style-h5` + `text-align-center` + `text-color-secondary`) could not be applied, so it was reverted to h5 to avoid a visual change.

Image compression: `compress_assets` format webp on 10 largest JPEG/PNG. Compressed: 74f20, 74f23, 74f21, 74f1f, 74ecc, 74f39, 74f38. Failed (converted larger than original): 74f1b, 74f24, 74f25. Second pass (avif, 16 assets) task c0ad6e75-... ended `failed`.

## Skipped / manual items
| Issue | URL(s) | Reason |
|---|---|---|
| Alt text on blog-card images | 48 pages | Inside Component "CMS Section / Blog / Related"; needs Designer: bind image Alt to post Name |
| `rel="noopener noreferrer"` | 87 pages | Navbar/Footer Component links (Kasper booking, Google Maps, Instagram, remedo.io, clerri) — Designer only |
| Component headings h5/h6 | Blog Related cards (h5), other Component headings | Designer only |
| Home / patient-resources / savings-plan pricing h3 -> h5 ("$348/year") | 3 pages | Not yet done |
| Meet Us, First Visit, blog listing, static location pages, Manalapan template, pricing h3->h5 (home, patient-resources, savings plan) | see crawl | Not done: Webflow API rate limit (429 on get_all_elements) — retry later |
| Size attributes | all pages | Component/CMS images cannot be set via API |
| /for-patients/insurance 404 | live | Not published; staging only per rule. Needs a decision |

## Platform limitations encountered
- Published to staging twice (webflow.io only); live domains untouched.
- Custom attributes / rel on Component-nested elements do not publish.
- CMS binding inside Components not possible.
- Webflow API returned 429 on `get_all_elements` after repeated page dumps.
- Chromium in the sandbox does not trust the proxy CA, so no rendered visual comparison was possible.

## Errors / escalations
- `get_all_elements` Old Bridge template: `GET /v2/assets returned 429`.
- AVIF compression task c0ad6e75-5d29-455a-9df4-6591d0af6457: status failed (no reason returned).

## Next steps outside MCP scope
- Designer work on Navbar/Footer/Blog Related Components (alt text, rel, heading levels).
- Screaming Frog export to confirm H2 duplicate, 3xx and 4xx items.
- Decide on publishing the Insurance page and on the live publish after review.

## Reference: key IDs
- Site ID: 67bf5f35cea87b89abb74e35
- Staging subdomain: wond-dinah.webflow.io
- Custom domain IDs (do NOT publish to): 67f061d7c4eac1e8edb8aa16 (www.polishedpd.com), 67f061d7c4eac1e8edb8aa0e (polishedpd.com)
- Page IDs: Home 67bf5f35cea87b89abb74e36, Contact ...e49, Meet Us ...e44, Blog Posts tpl ...e4e, Services tpl ...e50, Service Categories tpl ...e4f, Freehold tpl 6983378ff62bcab01181218b, Old Bridge tpl 6983448e9565e7f2fa2040ac, Manalapan tpl 6a01a04429887e78eafdfab9, Holmdel tpl 6a9684b2d78e5d2128a99aca, Insurance 6aaaa8d4946bbc1a182e8937
