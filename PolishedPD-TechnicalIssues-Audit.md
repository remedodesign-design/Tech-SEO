# PolishedPD — Technical Issues Audit (live crawl, 2026-09-29)

Site: www.polishedpd.com — Webflow Site ID 67bf5f35cea87b89abb74e35
Source: Sitemap crawl (89 URLs) + Asana ticket 1218698168907453. Screaming Frog export was not available.
Status: AUDIT ONLY. No changes have been made to Webflow yet.

| Ticket issue | Live crawl result | Root cause | MCP-fixable? |
|---|---|---|---|
| Images: Missing Alt Text | 48 pages. Blog-card images render `alt=""`. Three cards (sensory-friendly-dental-visit, dental-care-tips-for-children-with-adhd, pacifier-or-thumb) appear on 45 pages | Image lives inside Component "CMS Section / Blog / Related" (and the blog list card). Component has no alt prop | No — CMS binding inside a Component fails via API. Needs Designer: bind image Alt to the post Name field |
| Images: Missing Size Attributes | 89/89 pages have images without width+height (logo SVG, avif images, blog cards) | Webflow output; most are Component / CMS-bound images | Partly — only page-level static Image elements |
| Images: Over 100 kB | Not measured (needs size per asset) | — | Yes via data_assets_tool compress_assets, list needed |
| Security: Unsafe Cross-Origin Links | 87 pages. Repeating links: polishedpediatric.meetkasper.com/schedule-appointment (211), maps.app.goo.gl (175), instagram.com/polishedpediatricdentistry (87), remedo.io (87), member.clerri.com (9) | target=_blank without rel in Navbar/Footer Components | No — rel on Component-nested links does not publish. Needs Designer |
| H2: Duplicate | 0 pages with identical H2 text found | — | Nothing to fix from crawl (SF may be counting differently) |
| H2: Non-Sequential | 80 pages skip heading levels (e.g. home: h3 -> h5; meet-us: h2 -> h4, h4 -> h6; blog, contact, patient-resources) | h5/h6 used for styling in Components / sections | Mixed — page-level headings can be retagged; Component ones need Designer |
| Response Codes: Internal Redirection (3xx) | Internal links checked without following redirects: 0 found | — | Re-check in SF export (non-www links may be the source) |
| Response Codes: External Client Error (4xx) | https://www.cdc.gov/oralhealth returns 403 to bots (linked from post dental-sealants-when-to-check-marlboro-nj) — likely a bot block, not broken | — | Manual review |

## Important finding outside the ticket list
`/for-patients/insurance` returns **404 live** and is linked from the nav on most pages (first seen linked from the home page, meet-us, colts-neck). In Webflow the page "Insurance" (ID 6aaaa8d4946bbc1a182e8937, created 2026-09-16, updated 2026-09-26) is not draft and shouldPublish=true, but is missing from the live sitemap. Likely needs a publish or a check of the page's publish state. Not touched, as publishing is a live-site action needing approval.

## Not verified
Nothing has been changed, so nothing has been verified live.
