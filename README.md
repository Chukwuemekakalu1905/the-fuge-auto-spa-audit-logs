# thefugeautospa.com — Audit Logs

Victor Kalu-Benjamin
November 25, 2026

Raw logs from live checks I ran against https://www.thefugeautospa.com on November 25, 2026. Every log file records the exact command, a UTC timestamp, and the verbatim output, so anyone can re-run each check and compare results.

Scope: read-only probes only — HTTP GET/HEAD requests, DNS lookups, and TLS handshakes against the public site. No forms were submitted, no bookings were made, nothing was modified.

## How to read this repo

- `FINDINGS.md` — the pass/fail summary, one line per check.
- `headers/` — full response headers for every main page, plus a security-header presence matrix, cookie check, and compression/cache check.
- `dns/` — A, AAAA, MX, NS, TXT, CAA, SPF, DMARC, DKIM selectors, DNSSEC, and common-subdomain lookups.
- `tls/` — certificate details, protocol versions accepted, and redirect chains (http→https, apex→www).
- `paths/` — status code and response size for sensitive, backup, config, and invented paths, plus path-normalization behavior.
- `forms/` — form actions, field inventories, and the access-key values found in page source.
- `scripts/` — external script/embed inventory per page, source-map checks, and mixed-content scan.
- `content/` — prices, address, membership, review, and booking-page extracts from the site, with a Booksy cross-check.
- `methods/` — HTTP method handling and CORS behavior.
- `evidence/` — earlier raw check outputs from the same audit.

## Screenshots

Four PNG screenshots accompany this audit (test-results table, raw terminal output, adversarial probe results, adversarial raw output). They are attached separately with the build notes; the text logs in this repo contain the same underlying data.

## Notes on method

- DNS A-record answers in `dns/` show 198.18.15.x addresses. That range is a synthetic answer from the network resolver used during the audit, not the site's published records; NS, MX, and TXT answers are the authoritative records. AAAA, CAA, SPF, DMARC, and DKIM absences are recorded as observed.
- The Booksy business page rejected a direct text fetch during this audit. Booksy figures in `content/booksy.txt` come from dated search-result snippets of the listing and are labelled as such.
