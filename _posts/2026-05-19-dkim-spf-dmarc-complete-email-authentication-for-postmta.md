---
title: "DKIM SPF DMARC: Complete Email Authentication for PostMTA"
date: 2026-05-19
tags: ["email", "smtp", "devops", "postmta"]
---

## DKIM, SPF & DMARC: Complete Email Authentication Setup for PostMTA

Email authentication is non-negotiable for deliverability. Here's how to configure it properly with PostMTA.

### DNS Records You Need

**SPF Record** (TXT record for your sending domain):
```plaintext
v=spf1 include:_spf.postmta.com ~all
```

**DKIM Record** (TXT record — PostMTA generates the key):
```plaintext
postmta._domainkey IN TXT ( "v=DKIM1; k=rsa; p=YOUR_PUBLIC_KEY" )
```

**DMARC Record** (TXT at _dmarc.yourdomain.com):
```plaintext
v=DMARC1; p=quarantine; rua=mailto:dmarc@yourdomain.com; pct=100
```

### PostMTA Authentication Features

- **Automatic DKIM signing** — Per-domain keys, rotated automatically
- **SPF alignment** — Strict alignment mode available
- **DMARC reporting** — Aggregate reports in dashboard
- **Failover handling** — Route around ISP blocks

### Testing Your Setup

Use these tools to verify:
1. mail-tester.com — Full spam score analysis
2. mxtoolbox.com — DNS record checks
3. postmta.com/dashboard — Delivery analytics

[Configure PostMTA authentication →](https://postmta.com)


*[Learn more about PostMTA](https://postmta.com)*