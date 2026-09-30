You are picking up technical SEO work for the client PolishedPD (polishedpd.com), a pediatric dental practice on Webflow. Another Claude session did the work below. Continue from the open items. Read `PolishedPD-TechnicalIssues-Handoff.md` in this repo for detail if you need it.

## Context
- Asana task: https://app.asana.com/1/130785860963553/project/1206884437382226/task/1218698168907453 (Technical Issues Fix, P1, assignee Arpan Debnath). It lists 8 issues: images over 100 kB, images missing size attributes, unsafe cross-origin links, duplicate H2, images missing alt text, non-sequential H2, external 4xx, internal 3xx.
- Source checklist: Google Sheet id 1O9NPMHj7KmcG90N7Iza346pZ6UX9c0vEByFb6jyI7AQ, tab "Dev Checklist" (Screaming Frog crawl of 2026-08-15). Columns: A Priority, B Issue, C URL, D Current Value, E Recommended Fix, F Status, G Notes, H Proposed Fix Copy. Statuses in F and G have been filled in for rows 2-199 (Done / Partial / Not done / Not needed). Keep them current.
- Webflow site: "DINAH | Polished Pediatric Dentistry", site ID 67bf5f35cea87b89abb74e35. Live domains: polishedpd.com and www.polishedpd.com (custom domain IDs 67f061d7c4eac1e8edb8aa0e and 67f061d7c4eac1e8edb8aa16). Staging: wond-dinah.webflow.io.
- Repo: remedodesign-design/tech-seo, branch `Polished-PD`, draft PR #1. The handoff file and this prompt live there.

## Hard rules
1. Nothing goes live. Work and publish on staging only: `publish_site` with `publishToWebflowSubdomain: true` and `customDomains: []`. Never publish to the custom domains. Do not use `publish_collection_items`. Live goes out only when the user approves. After each staging publish, confirm live is unchanged.
2. Call `webflow_guide_tool` once at the start of a Webflow session. Element tools take `siteId` and `pageId` at the top level; tool argument names differ from the docs (`set_heading_level` takes `id` and `heading_level`, `set_style` takes `id` and `style_names`, collection tools take `collection_id` and `request`). Probe with an empty call to see the required keys.
3. Verify on the staging URL by curl and grep of the rendered HTML, not only the API reply. If the network policy blocks a host, tell the user.
4. Retagging a heading changes how it looks, because Webflow tag styles differ (h4 is 2rem/500, h3 is 2.5rem/400). Keep the look with `set_style` and the matching `heading-style-hN` class, base class first, for example `["heading-style-h6","text-color-secondary"]`. Three-class combos are not available; revert if a style cannot be kept.
5. The Webflow API returns 429 if many `get_all_elements` or asset calls run close together. Space them out. Big CMS bodies must be sent in full; generate the JSON with code, do not retype it.
6. Do not skip or invent copy. Do not touch a slug without approval.

## What has been done (all on staging only, live unchanged)
- Audit: crawled all 89 sitemap URLs. Most findings sit in shared Components (Navbar, Footer, Blog Related, FAQ card), which the API cannot edit.
- Headings (retagged, look preserved): Contact Email/Phone/Office h4 to h3; Blog Posts template "Share" h5 to h3; FAQ subtitle h6 to h3 in the Services, Service Categories, Freehold, Old Bridge and Holmdel templates; Freehold main page two card headings h5 to h2; Colts Neck H2 shortened.
- Images: 7 large JPEG/PNG compressed to WebP (8.5 MB to 3.9 MB). The AVIF batches stalled and failed.
- CMS (Blog Posts, Services, Manalapan, Holmdel collections):
  - Alt text set on 16 images (main and thumbnail fields for 11 blog posts, 3 Manalapan items, Holmdel, plus the inline sealant table image in the "how long do sealants last" post body). Setting alt re-hosted these images as new assets, so the library has duplicate copies.
  - Toothpaste post title (`name`) shortened to 54 chars. Pacifiers post `h1-heading` changed so H1 differs from title. Meta descriptions (`post-summary2`, Manalapan `meta-description`) shortened to 122-135 chars.
  - Duplicate H2s: four service pages got their own `differentiators-heading` and `timeline-sub-heading-1`; the Manalapan sealants H2 and the emergency blog post H2 were reworded. Cavities post H2 shortened to 63 chars.
- Sheet: Dev Checklist F and G filled in. Handoff file and PR updated.

## Findings not yet actioned
- Broken script (Screaming Frog external 4xx): https://cdn.jsdelivr.net/gh/wonderistweb/library/text-animation_v2.js returns 404. Location unknown.
- Redirecting URLs (Screaming Frog 3xx): polishedpd.com/ (non-www), two birdeye.com/embed/v7/... embeds (final: widgets-v7.birdeye.com/api/embed/v7/...), unpkg.com/split-type (pin split-type@0.3.4/umd/index.min.js), www.clerri.com/ev4g, www.kleer.com/ev4g, maps.app.goo.gl/vvvAvHVoAuDJNZNQ7.
- /for-patients/insurance returns 404 on live and the nav links to it. Page id 6aaaa8d4946bbc1a182e8937 shows draft false and shouldPublish true in Webflow. cdc.gov/oralhealth returning 403 is a bot block; ignore.

## What to do next, in order
1. Find where the jsdelivr script loads (site custom code via `data_scripts_tool`, page custom code, embeds). Re-fetch site custom code right before writing. Replace or remove it and check nothing depends on it.
2. Update the 3xx URLs wherever they are editable through the API (custom code, embeds). List those that need Designer.
3. Images: 37 still over 100 kB (17 WebP, 17 AVIF, 3 JPEG). Re-export outside Webflow and replace, or compress in the Assets panel. Offer to clean the duplicate assets only with the user's OK.
4. Remaining heading skips: Meet Us (h2 to h4/h5/h6), First Visit, Patient Resources, Polished Savings Plan (pricing h3 to h5, same on Home), blog listing, Manalapan collection template and static location pages, Colts Neck FAQ h4 items, and the Holmdel emergency H2 "Why Holmdel Families Turn to Polished Pediatric Dentistry for Emergencies" (73 chars, in the Holmdel `body`, collection 6a9684b2d78e5d2128a99ac4).
5. Designer-only items to hand to a person: FAQ card Component question heading h5 to h4; Blog Related Component card headings h5 to h3, and bind image Alt there; `rel="noopener noreferrer"` on Navbar/Footer links (Kasper booking, Google Maps, Instagram, remedo.io, clerri); width and height on Component and CMS images; "Still have questions? Give us a call!" h5 in the Services template needs a custom class combo.
6. Cannot be done through the API: security response headers (X-Content-Type-Options, Referrer-Policy, Content-Security-Policy, X-Frame-Options) and protocol-relative links. Mark as hosting or custom-code work.
7. Content decisions, not to be done without approval: low-content pages (home 0 words, Freehold 181 words) and readability.
8. After each batch: verify on staging, update the Dev Checklist F and G cells, update the handoff file, commit and push to `Polished-PD`.

## Needs the user's approval, do not do
- Publishing the Insurance page (approval pending).
- Any publish to the live domains.

## Reporting
Report only what is done and what needs a person: items to do in Designer and decisions. Keep the user's Asana task and Sheet current.
