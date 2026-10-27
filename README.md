# CyberSpartans Website (cyberspartans.co.uk)

The cybersecurity and cloud consultancy site for Saleem Yousaf.

## Files

```
cyberspartans-site/
├── index.html          ← Home (About-led: who we are, four-pillar brand image, services, capabilities, connect)
├── services.html       ← Services in detail (cyber/cloud, Cymulate, Intruder, audits + FinOps)
├── capabilities.html   ← BreachForge (live, reference standard, with built-in threat intelligence)
├── logo.png            ← Spartan helmet logo (emerald)
├── sitemap.xml         ← XML sitemap (apex URLs)
├── robots.txt          ← Crawl rules, points to the sitemap
├── CNAME               ← Custom domain (cyberspartans.co.uk) - do not delete
└── README.md           ← This file
```

Each page is self-contained: styles are inline, so there is no shared stylesheet.
The old shared.css (red theme) has been retired and removed from the repo. No page references it.

Design: dark slate base with an emerald accent (#2f9e6f). Fonts Syne (display), Inter (body), DM Mono (mono).
The homepage hero is the Aegis Core brand image: a CyberSpartans helmet at the centre with the four
pillars orbiting it (Cyber, Cloud, Risk, Cost), each in its own colour.

## Positioning

CyberSpartans is a consultancy, not a product company (yet). The site sells:

1. Cybersecurity & cloud expertise (core practice)
2. Continuous security validation (reselling Cymulate)
3. Vulnerability management (via Intruder)
4. Audits & FinOps

BreachForge is a CyberSpartans product and is live, behind a secure login at breachforge.co.uk.
It is the reference standard the practice measures commercial BAS tools against, and it carries
built-in threat intelligence from open-source feeds (CISA KEV, MITRE ATT&CK, AlienVault OTX, abuse.ch).
There is no separate TI Portal: the threat intelligence lives inside BreachForge.

Contact is via info@cyberspartans.co.uk or LinkedIn (no demo form).
Credentials shown: AWS, Azure, CEH.

## Navigation

About (home) → Services → Capabilities → Connect (email + LinkedIn).
The nav and footer also carry cross-property links to breachforge.co.uk and saleemyousaf.co.uk.

## Updating on GitHub Pages

1. Edit the file on GitHub (pencil icon) or upload a replacement
2. Commit changes
3. Site rebuilds in 1-2 minutes
4. Do NOT delete the CNAME file - it keeps cyberspartans.co.uk pointed at this repo

## SEO foundations in place

- Unique title and meta description per page
- Canonical URLs (apex only, no www, no trailing slash)
- Open Graph and Twitter card tags for social sharing
- Author meta (Saleem Yousaf) on every page
- JSON-LD on every page: Organization (Cyber Spartans Ltd), Person (Saleem Yousaf, founder),
  and SoftwareApplication (BreachForge), with the four-service offer catalog and full sameAs list
- Static footer cross-linking to saleemyousaf.co.uk and breachforge.co.uk (passes link value)
- sitemap.xml and robots.txt

Still to do: submit sitemap.xml in Google Search Console and complete Search Console verification.

## URL rules

All three properties are apex only: https://cyberspartans.co.uk, https://breachforge.co.uk,
https://saleemyousaf.co.uk. No www, no trailing slash, in any URL, canonical, og:url, schema field,
or content. www variants will not resolve and must never be used.

## Tone rules

No em or en dashes. No buzzwords (orchestrated, leveraged, transformative, seamless, etc).
No year-count claims; use "securing UK government, critical national infrastructure, and global enterprise".
Written plainly, like explaining to a peer.
