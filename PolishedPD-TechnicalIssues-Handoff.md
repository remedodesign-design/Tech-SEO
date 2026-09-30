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

## Round 2: Screaming Frog items (2026-09-30) — all applied to staging only
Publishing: `publish_site` with `publishToWebflowSubdomain: true` and no custom domains. Live polishedpd.com re-checked after publish: unchanged.

| # | Item | Change (CMS/element) | Verified on staging |
|---|---|---|---|
| 1 | Toothpaste post title over 60 chars | Blog `name` -> "Toothpaste Amounts by Age: Grain-of-Rice vs. Pea-Sized" (54 chars, from the checklist's proposed copy) | Title shows 54 chars |
| 2 | Images over 100 kB | 7 JPEG/PNG compressed to WebP (8.5 MB -> 3.9 MB). AVIF batches stalled or failed. 37 images remain >100 kB in the library: 17 WebP, 17 AVIF, 3 JPEG | Not resolved, see manual list |
| 3 | Missing alt (16 images) | Alt set in CMS on main + thumbnail images for 11 blog posts, 3 Manalapan items, Holmdel item; inline sealant table image alt set in post body | No empty alt on the tested pages. Note: setting alt re-hosted each image as a new asset (duplicate files in the library) |
| 4 | Duplicate H2s (8 pages) | Services: `differentiators-heading` and `timeline-sub-heading-1` made service-specific; Manalapan sealants H2 and emergency blog H2 reworded | Cross-page duplicates gone on the 8 pages |
| 5 | Meta description over 155 (5 pages) | `post-summary2` shortened on 4 posts; Manalapan dental-cleaning `meta-description` shortened | 122 to 135 chars |
| 6 | H2 over 70 chars | Colts Neck H2 reworded (static page, `set_text`); cavities post H2 reworded | Both under 70 |
| 7 | Pacifiers post title = H1 | `h1-heading` -> "When Should Kids Stop Pacifiers and Thumb Sucking?" | H1 differs from title |
| 8 | Teething meta over 985px | Meta shortened to 123 chars | 123 chars |
| 9 | Freehold non-sequential H2 | Two card headings h5 -> h2 with `heading-style-h5` kept | No skips |

Not fixable through the API (still open): related-post card headings (h2 -> h5) in the Blog Related Component, FAQ card headings (h3 -> h5) in the FAQ card Component, Holmdel "Why Holmdel Families Turn to Polished Pediatric Dentistry for Emergencies" H2 is 73 chars (not on the list), Colts Neck FAQ h4 after h2.

## Round 3: Open items 1-2, scripts and redirecting URLs (2026-09-30) — staging only
Published with `publishToWebflowSubdomain: true`, no custom domains. Live polishedpd.com re-checked: unchanged (still has the old script and URLs).

| Item | Change | Verified on staging |
|---|---|---|
| 404 script `cdn.jsdelivr.net/gh/wonderistweb/library/text-animation_v2.js` | Found in site footer custom code. Removed. Nothing depended on it: no element on any of the 89 sitemap pages uses `text-split` or `js-line-animation` (only the CSS in site head mentions them) | 0 references on home, Meet Us, Savings Plan |
| `unpkg.com/split-type` (3xx) | Pinned to `split-type@0.3.4/umd/index.min.js` (site footer). Kept because removal was not requested | Present, returns 200 |
| BirdEye floating widget embed (site footer) | `birdeye.com/embed/v7/...` -> `widgets-v7.birdeye.com/api/embed/v7/...` | New URL on all pages |
| BirdEye embed, Meet Us page HTML embed (`/11/` id) | Same host change | 2 new-URL refs on /about/meet-us, 0 old |
| `www.kleer.com/ev4g` (Savings Plan "Fill Out Our Form" button) | Component instance Link prop set to `https://member.clerri.com/?slug=EV4G`, the same URL the other buttons on the page use | 0 kleer refs on staging |

Still open from the 3xx list (Designer only, in Navbar/Footer Components): `maps.app.goo.gl/vvvAvHVoAuDJNZNQ7` (87 pages) redirects to a google.com/maps/place URL. `www.clerri.com/ev4g` was not found in any rendered page; the only clerri links are `member.clerri.com/?slug=EV4G`. `polishedpd.com/` (non-www): a domain-level redirect, not a page link.

## Round 4: remaining heading skips (2026-09-30) — staging only
Retagged with the matching `heading-style-hN` class so the look is kept. Published to staging only; live re-checked and unchanged. Skips counted on rendered HTML, staging vs live:

| Page | Change | Result |
|---|---|---|
| First Visit | four h4 and four h5 -> h3 (`heading-style-h4` / `heading-style-h5`) | 3 skips -> 1 (h2 -> h6 left, in a Component) |
| Meet Us | Sensory Play h4 -> h3; other static retags | 7 skips -> 6. The team cards (h4 name, h6 role) sit in a list that did not take the edit on publish, so they stay |
| Blog listing | card title h5 -> h3 (`heading-style-h5`, `text-color-secondary`) | 1 -> 0 |
| Colts Neck, Monroe, East Brunswick | six FAQ h4 -> h3 (`heading-style-h4`) each | 1 -> 0 |
| Holmdel (static) | six FAQ h4 -> h3, final h5 -> h4 | 1 -> 0 |
| Old Bridge (static) | three h5 -> h3 (`heading-style-h5`, `text-color-secondary`) | 1 -> 0 |
| Manalapan (static) | h5 -> h3 (`heading-style-h5`, `text-color-secondary`) | 1 -> 0 |
| Patient Resources | h6 -> h3 (`heading-style-h6`, `text-color-secondary`) | Static heading fixed; 3 skips remain, from Component pricing headings (h3 -> h5) |

No change needed: Home, Manalapan template and Matawan already have no skips in their own headings (Home and the template skips come from Components). Left as is: Meet Us "We Make Dental Care Fun" h5 (its look needs `heading-style-h5` + `text-align-center`, and that combo would not apply), Savings Plan h6 with three classes (`text-color-secondary`, `primary`, `blsck`), and the Holmdel emergency H2 (73 chars, in the CMS body).

Images: not re-run. 37 remain over 100 kB: 17 WebP, 17 AVIF and 3 JPEG that grew when converted. Compression replaces the file in place with no copy of the original, and re-compressing WebP/AVIF will not help. These need re-exporting outside Webflow.

## Round 5: Component internals are editable through the API (2026-09-30) — staging only
Correction to earlier notes: elements inside Components can be read and written with `scope_component_id` (on the element tool, and `data_component_tool` / `data_component_props_tool`), and the changes publish. Retagged with the look kept:

| Component | Change |
|---|---|
| FAQ card (question) | h5 -> h4, `heading-style-h5` |
| CMS Section / Blog / Related (card title) | h5 -> h3, `heading-style-h5` + `text-color-secondary` |
| Section / Membership (three pricing headings) | h5 -> h4, `heading-style-h5` |
| Team Card | name h4 -> h3 (`heading-style-h4` + `text-color-secondary`), role h6 -> h4 (`heading-style-h6`) |

Staging vs live heading skips: Home 3 -> 0, Patient Resources 3 -> 0, Contact 1 -> 0, blog listing 0, services 0, Meet Us 7 -> 1, Savings Plan 4 -> 1, First Visit 3 -> 1. Left: Meet Us h2 -> h5 (needs a two-class combo Webflow would not apply), Savings Plan h3 -> h6 (three classes), First Visit h2 -> h6.

Not possible: binding Blog Related image alt to the post name (CMS fields are not offered as sources inside the Component, so it stays a Designer task). `rel="noopener"`: judged not needed, modern browsers default `_blank` links to noopener.

## Round 6: rel on external links, image size attributes (2026-09-30) — staging only
- **rel: works.** Using the `rel` **custom attribute** (`set_attributes`, not the link setting, which is dropped on publish) on link elements inside Components publishes correctly. Set `rel="noopener noreferrer"` on Navbar and Footer links (icon links, scheduling buttons, social, contact, remedo.io), the Section / Footer and Service Masthead Kasper buttons, Button / Booking and the three Membership clerri buttons, and `rel="noopener"` on the shared Button / Global and Button/Primary components. Also set on the Home and Contact map links. Screaming Frog-style count of `target="_blank"` links without rel across the 89 staging pages: several hundred -> 30.
- **Still without rel (30 links, page-level, per page or template):** Kasper `w-inline-block` links and Google Maps `data-button-style="secondary"` links on the services, service-categories and Manalapan item pages; three `https://www.polishedpd.com/` blank links; one on terms-and-conditions and two on /post/what-causes-cavities-in-kids. These sit in page or CMS-template link elements not yet swept.
- **Image width/height: not possible through the API.** `set_attributes` on an Image element (page-level, Navbar logo, Blog Related image) returns OPERATION_FAILED for width and height. Designer-only (Webflow reserves these attributes).
- **Blog Related image alt binding: not possible.** CMS fields are not offered as alt sources inside the Component; Designer-only.

## Round 7: finishing the partial items (2026-09-30) — staging only
Heading skips, all 89 staging pages counted on rendered HTML: about 80 pages at the start -> 6.
- Fixed: Meet Us "We Make Dental Care Fun" h5 -> h3 (`heading-style-h5`, centered with the site's `data-text-align-center="true"` attribute); Section / FAQs subtitle h6 -> h3 (fixes First Visit); Service Categories template card headings h4 -> h3 (`heading-style-h4`, `text-color-secondary`); FAQ question headings h5 -> h4 in the Freehold, Old Bridge and Holmdel item templates (their own `Heading` / `Heading 5` classes keep the look).
- Still open (6 pages): Savings Plan h3 -> h6 (look depends on `.text-color-secondary.primary.blsck`; Webflow will not apply a four-class combo, and an h4 would lose the uppercase h6 look, so it was reverted); `/services/emergency-dentistry`, `infant-dentistry`, `sedation-dentistry` (h2 -> h6) and `/service-categories/preventive-dentistry`, `restorative-dentistry` (h2 -> h4) come from `<h6>` / `<h4>` tags inside CMS rich text bodies, where a retag changes the look. Decision needed.

rel on external links: 30 -> 7 `target="_blank"` links without rel. Added `rel="noopener noreferrer"` to the Kasper buttons on the Services, Service Categories and Manalapan item templates, the Google Maps button on all eight location pages, and `noopener` on absolute polishedpd.com links on the Manalapan page. Left: two same-origin `href="/"` buttons (Terms, Privacy), three same-origin `polishedpd.com` links inside blog post bodies, and one Kasper link in the dental-sealants CMS body. None of these is cross-origin except the last, which sits in rich text.

Still Designer-only or not possible: image width/height, Blog Related image alt, the Google Maps short-link redirect (href not readable through the API), 37 large images.

## Round 8: housekeeping, Holmdel H2, protocol-relative link, re-check (2026-09-30) — staging only
- Dev Checklist F and G updated for all rows from a fresh staging check of the 89 sitemap pages (Done 128, Not needed 44, Not done 18, Partial 8).
- Protocol-relative link: the footer `//instant.page/5.2.0` now uses `https://`. It was the only one on the site.
- Holmdel emergency H2 (73 chars, in the CMS `body`): now "Why Holmdel Families Choose Us for Pediatric Dental Emergencies" (63 chars). Wording is not from the sheet's Proposed Fix Copy column, which has no entry for it. The `header-title` field still holds the old text but does not render as an H2.
- Home "0 words": the crawl row is the non-www URL `https://polishedpd.com/`, a 301 redirect stub. The real home page has about 795 words. Marked Not needed. Freehold (181 words) and readability were left as content decisions for the SEO team.
- Insurance page and any live publish: not touched.
- Still open: 3 H2s over 70 characters (Manalapan restorative dentistry page, "tips-to-help-picky-eaters" post, "infant-first-dentist-visit" post), 6 pages with one skipped heading level (rich-text tags or a class combo Webflow will not apply), image size attributes, Blog Related image alt, Google Maps short-link redirect, 37 images over 100 kB, security response headers.
- Visual check: Chromium screenshots of live vs staging on 10 pages. Blog listing, Holmdel emergency page and Home compared by eye and match apart from expected copy changes (blog card excerpts shortened in round 2, the new Holmdel H2) and floating widgets. The remaining crops were not reviewed; a permission block stopped the last image-crop step.
