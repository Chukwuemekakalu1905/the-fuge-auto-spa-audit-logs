# Findings — thefugeautospa.com, November 25, 2026

Observed results only. Each line points to the raw log that backs it.

## Failing checks

1. **Lead forms cannot succeed.** Homepage, Contact, and Fleet forms post to Web3Forms with the access key still set to the literal placeholder `{{WEB3FORMS_ACCESS_KEY}}`. (`forms/`)
2. **No Content Security Policy** on any page checked. (`headers/header-matrix.txt`)
3. **Wildcard CORS.** Every response carries `Access-Control-Allow-Origin: *`, including on a preflight-style OPTIONS request with a foreign Origin. (`headers/`, `methods/cors.txt`)
4. **No real 404s.** 58 probed paths — including `/.env`, `/.git/config`, `/.aws/credentials`, `/backup.zip`, `/db.sql`, config and backup filenames, and invented paths — all return HTTP 200 with the identical 103,597-byte homepage. No sensitive file content was exposed; the failure is that broken and bogus URLs are indistinguishable from real pages. (`paths/`)
5. **No real `security.txt`.** `/.well-known/security.txt` returns the homepage HTML. (`paths/security-txt-body.txt`)
6. **No SPF record.** The domain's only TXT record is a Google site-verification string. (`dns/spf.txt`, `dns/apex-TXT.txt`)
7. **No DMARC record.** (`dns/dmarc.txt`)
8. **No DKIM record** on any of seven common selectors checked (google, default, brevo, selector1, selector2, mail, k1). (`dns/dkim-all-selectors.txt`)
9. **No CAA record.** (`dns/apex-CAA.txt`)
10. **No DNSSEC** (no DS, no DNSKEY). (`dns/dnssec.txt`)
11. **No cross-origin isolation headers** (COOP/COEP/CORP absent on all pages). (`headers/header-matrix.txt`)
12. **Third-party scripts without SRI.** Google Tag Manager and a Google Maps embed load on the homepage; no `integrity` attributes anywhere. Inline script count on the homepage: 8 script tags total. (`scripts/home.txt`)
13. **Membership plans absent from the site.** No membership or Unlimited pricing content on the homepage, services, express-wash, or FAQ pages (one "no membership required" line on express-wash). (`content/memberships.txt`)
14. **Review claims in page source:** four homepage review cards each labelled "Rated 5 out of 5 stars"; reviews-page meta claims "5.0 stars on Booksy, 4.7 on Groupon, and five-star Google reviews." A Booksy listing snippet (crawled ~3 days prior) showed 4.7 with 12 reviews. (`content/reviews.txt`, `content/booksy.txt`)
15. **Booking page copy promises a calendar.** `/book/` text references live availability/calendar; the page itself loads the Booksy widget. (`content/book-page.txt`)

## Passing checks

1. **TLS 1.3 and 1.2 accepted; TLS 1.0/1.1 handshakes produced no successful negotiation.** Certificate: Google Trust Services (WE1), ECDSA P-256, valid Oct 31 2026 – Jan 29 2027, SAN `www.thefugeautospa.com`. (`tls/`)
2. **HSTS present** with `includeSubDomains; preload`, one-year max-age. (`headers/`)
3. **X-Frame-Options: DENY**, **X-Content-Type-Options: nosniff**, **Referrer-Policy: strict-origin-when-cross-origin**, **Permissions-Policy** restricting geolocation/microphone/camera. (`headers/header-matrix.txt`)
4. **HTTPS redirects work:** http→https and apex→www both 301 to the canonical https://www address; missing trailing slashes 308 to the slashed path. (`tls/redirect-chains.txt`)
5. **Write methods rejected:** OPTIONS, PUT, DELETE, PATCH, POST, and TRACE to `/` all returned 405 (status-only checks, no payloads sent). (`methods/http-methods.txt`)
6. **No cookies set** on homepage, book, contact, or reviews pages. (`headers/cookies.txt`)
7. **No mixed content:** the only `http://` references in page source are the SVG XML namespace identifier, not loaded resources. (`scripts/mixed-content.txt`)
8. **No source maps exposed:** all seven JS/CSS assets return the soft-404 homepage for their `.map` URLs, not real source maps. (`scripts/source-maps.txt`)
9. **Compression and caching sane:** Brotli served; HTML `max-age=0, must-revalidate`; hashed assets `immutable` with ETag. (`headers/compression-cache.txt`)
10. **Mail hosting present:** MX records point to Google Workspace. (`dns/apex-MX.txt`)
11. **robots.txt and sitemap present** and well-formed (17 URLs in sitemap-0.xml). (`paths/`, audit notes)

## Path-normalization observations

`/%2e%2e%2f` returned 400; `/%2e%2e/` returned no response (000); `//`, `/..;/`, and `/SERVICES/` returned the soft-404 homepage; `/services//` and `/services%2f` resolved to the real services page (49,265 bytes). (`paths/path-normalization.txt`)

## Unverified

- **Current live Booksy prices.** The Booksy business page rejected a direct fetch during this audit (HTTP 400). Search snippets of the listing (crawled ~3 days before the audit) show Head Light Restoration $80.00, Clay Bar $100.00+, Buff Wax $180.00+, address 615 W Ray Rd, Gilbert, 85233. Treat Booksy figures as snippet evidence, not a live read. (`content/booksy.txt`)
- **A/AAAA answers** in `dns/` include 198.18.15.x synthetic resolver answers from the audit network; they are not relied on for any finding above.
