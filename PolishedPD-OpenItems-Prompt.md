You are continuing technical SEO fixes for PolishedPD (Webflow). Read `PolishedPD-TechnicalIssues-Handoff.md` in this repo first. It lists what is done and the IDs you need.

Hard rules
- Work and publish on staging only: `wond-dinah.webflow.io`. Publish with `publish_site` using `publishToWebflowSubdomain: true` and `customDomains: []`. Never publish to `polishedpd.com` or `www.polishedpd.com`. Do not use `publish_collection_items`. Live goes out only after the user approves.
- Site ID: 67bf5f35cea87b89abb74e35. Call `webflow_guide_tool` once at the start.
- Verify every change on the staging URL (curl and grep the rendered HTML), and confirm live is unchanged.
- Retagging a heading changes its look, because tag styles differ. Keep the look with `set_style` and the matching `heading-style-hN` class, listing the base class first (for example `["heading-style-h6","text-color-secondary"]`).
- The Webflow API returns 429 if you dump many pages fast. Space `get_all_elements` calls out.

Open items, in priority order
1. Broken script: `https://cdn.jsdelivr.net/gh/wonderistweb/library/text-animation_v2.js` returns 404 (Screaming Frog 4xx). Find where it loads (site custom code via `data_scripts_tool`, page custom code, or an embed). Replace or remove it, and confirm nothing on the site depends on it. Re-fetch site custom code right before writing.
2. Redirecting URLs (Screaming Frog 3xx): `polishedpd.com/` (use the www URL in internal links), `birdeye.com/embed/v7/...` (two embeds, use `widgets-v7.birdeye.com/api/embed/v7/...`), `unpkg.com/split-type` (pin `split-type@0.3.4/umd/index.min.js`), `www.clerri.com/ev4g`, `www.kleer.com/ev4g`, `maps.app.goo.gl/vvvAvHVoAuDJNZNQ7`. Update where editable through the API; list what needs Designer.
3. Images over 100 kB: 37 remain in the asset library (17 WebP, 17 AVIF, 3 JPEG). API AVIF compression stalled and failed. Re-export them (Squoosh or similar) and replace the assets, or compress from the Webflow Assets panel. Setting CMS alt text also re-hosted images as duplicate assets; clean up duplicates only if the user agrees.
4. Heading skips still open:
   - Meet Us, First Visit, Patient Resources, Polished Savings Plan, blog listing, Home (pricing h3 to h5), Manalapan collection template and static location pages (h2 to h4 FAQ items).
   - Component-nested (Designer only): Blog Related card headings (h5 after h2), FAQ card question headings (h5 after h3). Change the FAQ card heading from h5 to h4 and the Blog Related card heading to h3.
   - Holmdel emergency page H2 "Why Holmdel Families Turn to Polished Pediatric Dentistry for Emergencies" is 73 characters (edit `body` in the Holmdel collection, id 6a9684b2d78e5d2128a99ac4).
5. Designer-only items: `rel="noopener noreferrer"` on Navbar/Footer Component links (Kasper booking, Google Maps, Instagram, remedo.io, clerri); width and height on Component and CMS images; alt text bound to the post Name in the "CMS Section / Blog / Related" Component (CMS alt is now set, so this is a backstop).
6. Not possible via the API: security response headers (X-Content-Type-Options, Referrer-Policy, Content-Security-Policy, X-Frame-Options) and protocol-relative links. Note for the client or handle through hosting or custom code.
7. Content decisions, not to be done without approval: low-content pages (home 0 words, Freehold 181 words) and readability.

Needs approval, do not do
- Publish the Insurance page (id 6aaaa8d4946bbc1a182e8937). It returns 404 on live and the nav links to it. Wait for the user's approval.
- Any publish to the live domains.

When done
- Update `PolishedPD-TechnicalIssues-Handoff.md` and the "Dev Checklist" tab (Google Sheet id 1O9NPMHj7KmcG90N7Iza346pZ6UX9c0vEByFb6jyI7AQ), columns F (Status: Done / Partial / Not done / Not needed) and G (Notes).
