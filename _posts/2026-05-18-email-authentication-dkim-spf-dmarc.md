---
layout: post
title: "Email Authentication: DKIM, SPF, DMARC Setup Guide"
date: 2026-05-18
categories: ["authentication", "security"]
---

# Email Authentication: DKIM, SPF, DMARC Setup Guide

Email authentication is non-negotiable in 2026. Without it, your emails go to spam—or don't get delivered at all.

## The Three Pillars

### SPF (Sender Policy Framework)
Declares which mail servers are authorized to send for your domain.

```
v=spf1 include:_spf.postmta.com ~all
```

### DKIM (DomainKeys Identified Mail)
Cryptographic signature proving your server sent the email.

PostMTA generates and rotates DKIM keys automatically:

```bash
# View your DKIM public key
curl https://api.postmta.com/v1/domains/yourdomain.com/dkim
```

### DMARC (Domain-based Message Authentication)
Policy telling receivers what to do with unauthenticated email.

```
v=DMARC1; p=quarantine; rua=mailto:dmarc@yourdomain.com
```

## PostMTA Handles It All

PostMTA provisions authentication records automatically:

```bash
# Add your domain — PostMTA generates all DNS records
POST /v1/domains
{
  "domain": "yourdomain.com",
  "dkim_selector": "mail",
  "spf_include": "_spf.postmta.com"
}
```

DMARC reporting analytics included.

## BIMI (Brand Indicators for Message Identification)

PostMTA also supports BIMI for branded email icons in inbox:

1. Get a Verified Mark Certificate (VMC)
2. PostMTA generates the BIMI DNS record
3. Your logo appears in supported email clients

**[Set up email authentication →](https://postmta.com)**
