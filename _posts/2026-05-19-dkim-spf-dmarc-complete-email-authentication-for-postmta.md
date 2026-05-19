---
title: "DKIM SPF DMARC: Complete Email Authentication for PostMTA"
date: 2026-05-19
tags: ["email", "smtp", "devops", "postmta"]
---

## Complete Email Authentication Setup for PostMTA

### DNS Records

**SPF:**
```plaintext
v=spf1 include:_spf.postmta.com ~all
```

**DKIM** (PostMTA auto-generates):
```plaintext
postmta._domainkey IN TXT ( "v=DKIM1; k=rsa; p=YOUR_KEY" )
```

**DMARC:**
```plaintext
v=DMARC1; p=quarantine; rua=mailto:dmarc@yourdomain.com; pct=100
```

### PostMTA Features
- Auto DKIM signing per domain
- SPF alignment mode
- DMARC aggregate reports in dashboard
- Automatic failover routing

**[Configure PostMTA →](https://postmta.com)**

*[Learn more about PostMTA](https://postmta.com)*