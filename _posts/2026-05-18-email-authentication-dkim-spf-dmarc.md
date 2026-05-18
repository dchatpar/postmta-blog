---
layout: post
title: "DKIM, SPF & DMARC: The Complete Email Authentication Guide for 2026"
date: 2026-05-18
permalink: /:title/
description: "Email authentication isn't optional anymore. Gmail and Yahoo now require SPF, DKIM, and DMARC for any sender above 5,000 daily recipients. Microsoft has require"
tags: [email, MTA, KumoMTA, SMTP, devops]
canonical_url: https://postmta.com/
---


Email authentication isn't optional anymore. Gmail and Yahoo now require SPF, DKIM, and DMARC for any sender above 5,000 daily recipients. Microsoft has required it for years. If your authentication is wrong, your mail goes to spam — or doesn't get delivered at all.

This guide covers every technical detail of email authentication.

---

## Why Email Authentication Matters Now More Than Ever

In 2024, Google and Yahoo introduced mandatory email authentication requirements:

- **SPF and DKIM required** for any domain sending more than 5,000 emails/day
- **DMARC required** for bulk senders (or your mail goes to spam)
- **BIMI** (Brand Indicators for Message Identification) lets you display your logo in inboxes — requires DMARC at `p=quarantine` or stricter

Without authentication, you're invisible to modern email systems. With it, you control your sender identity and protect against spoofing.

---

## SPF: Who Can Send For Your Domain

### How SPF Works

When your mail server receives a message `From: sender@example.com`, it looks up the SPF record for `example.com` in DNS. If the sending server's IP is listed, the message passes SPF. If not, it fails.

### SPF Record Syntax

```
v=spf1 ip4:162.222.226.207 include:_spf.google.com include:_spf.mailgun.org ~all
```

- `v=spf1` — Version identifier (always exactly this)
- `ip4:162.222.226.207` — Authorize specific IP addresses
- `include:_spf.google.com` — Include another domain's SPF record
- `~all` — Soft fail (treat failures as suspicious but don't reject). Use `-all` for hard fail once tested.

### Common SPF Mistakes

**Too many lookups:** SPF has a 10-DNS-lookup limit. Every `include:`, `a:`, `mx:`, `ptr:`, and `redirect` counts. Exceed this and SPF breaks silently.

```
v=spf1 include:server1.com include:server2.com include:server3.com 
       include:mailserver1.com include:mailserver2.com 
       include:sendgrid.net include:mailgun.org ~all

v=spf1 ip4:162.222.226.207 include:_spf.postmta.com ~all
```

**Including PTR records:** Never use `ptr:` — it's unreliable and counts as 2 lookups per lookup. Use `a:` or `ip4:` instead.

**Not including your web server:** If your application sends email (order confirmations, password resets), include your server IPs.

### SPF for Multiple Sending Sources

```
v=spf1 
  ip4:162.222.226.207          # Your KumoMTA server
  ip4:10.0.0.1                 # Your application server
  include:_spf.google.com       # Google Workspace
  include:sendgrid.net           # SendGrid (if you use it for some campaigns)
  ~all
```

---

## DKIM: Cryptographic Message Signing

### How DKIM Works

DKIM attaches a digital signature to every outbound message. The signature is created using your **private key** (stored on your mail server) and verified using your **public key** (published in DNS).

The signature covers the message headers and body — any tampering in transit breaks the signature.

### Generating DKIM Keys

```bash
openssl genrsa -out dkim_private.pem 2048
openssl rsa -in dkim_private.pem -pubout -out dkim_public.pem

chmod 600 dkim_private.pem
sudo mv dkim_private.pem /etc/kumomta/dkim/
```

### DKIM DNS Record

Publish your public key in DNS with a selector:

```
mail._domainkey.postmta.com IN TXT (
  "v=DKIM1; k=rsa; p=MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKB..."
)
```

- `mail` — Selector. You can use any selector name. Multiple selectors = multiple DKIM keys for key rotation.
- `p=` — Your public key (paste the contents of `dkim_public.pem` after the `-----BEGIN PUBLIC KEY-----` header)

### Verifying DKIM is Working

Send a test message and check the headers:

```
Received-SPF: pass (google.com: domain of postmaster@postmta.com designates 162.222.226.207 as permitted sender)
Authentication-Results: mx.google.com;
       dkim=pass header.i=@postmta.com header.s=mail header.b=ABC123;
       spf=pass google.com;
       dmarc=pass header.from=postmta.com
```

If you see `dkim=fail` or `dkim=neutral`, your signature isn't being added or DNS isn't publishing correctly.

### DKIM Key Rotation

Rotate your DKIM keys every 90 days:

1. Generate new key pair
2. Add new DKIM record with a new selector (e.g., `mail2`)
3. Wait 2 TTL cycles (48-72 hours) for DNS to propagate
4. Update KumoMTA to use new selector
5. Remove old selector DNS record after 2 weeks

---

## DMARC: Tying It All Together

### How DMARC Works

DMARC tells receiving servers what to do when SPF and DKIM both fail (or one fails). It also sends you XML reports about authentication results.

### DMARC Record Syntax

```
_dmarc.postmta.com IN TXT "v=DMARC1; p=quarantine; rua=mailto:dmarc-reports@postmta.com; pct=100; rf=afrf"
```

- `v=DMARC1` — Version (always exactly this)
- `p=quarantine` — Policy: `none` (monitor only), `quarantine` (mark suspicious as spam), `reject` (hard reject)
- `rua=mailto:...` — Aggregate reports (daily XML summary of authentication results)
- `pct=100` — Percentage of mail to apply policy to (start with 10-25%, ramp to 100%)
- `rf=afrf` — Report format: `afrf` (Authentication Failure Reporting Format) or `iodef` (IODEF for industry standard)

### DMARC Alignment

For DMARC to pass, either SPF or DKIM must **align** with the From: header domain:

**SPF Alignment:** The `MAIL FROM` domain (used in SMTP envelope) must match or be a subdomain of the `From:` header domain.

**DKIM Alignment:** The `d=` domain in the DKIM signature must match or be a subdomain of the `From:` header domain.

```
From: postmaster@postmta.com
MAIL FROM: mailgun.org (sends for postmta.com via Mailgun)
```

### Reading DMARC Reports

DMARC aggregate reports are XML sent to your `rua:` email address. They tell you:
- How many messages passed/failed SPF, DKIM, DMARC
- Which IPs are sending for your domain (authorized and unauthorized)
- Which sources are failing authentication

Use a DMARC report parser like:
- **dmarcian.com** (has a free analyzer)
- **Kumomta DMARC Reporter** (open source)
- **MXToolbox DMARC Analyzer**

### Progressive DMARC Deployment

**Phase 1 — Monitor (2-4 weeks):**
```
v=DMARC1; p=none; rua=mailto:dmarc@postmta.com; pct=25
```

**Phase 2 — Quarantine (2-4 weeks after verifying all legitimate sources authenticate):**
```
v=DMARC1; p=quarantine; rua=mailto:dmarc@postmta.com; pct=50
```

**Phase 3 — Full Deployment:**
```
v=DMARC1; p=reject; rua=mailto:dmarc@postmta.com; pct=100
```

---

## BIMI: Display Your Logo in Inboxes

BIMI (Brand Indicators for Message Identification) adds your brand logo next to your emails in supporting email clients (Gmail, Apple Mail, Outlook.com).

**Requirements for BIMI:**
1. DMARC at `p=quarantine` or `p=reject`
2. Valid SPF and DKIM
3. SVG logo published at a known URL
4. VMC (Verified Mark Certificate) — optional but required for Gmail (EV certificates from DigiCert or Certum)

**BIMI DNS Record:**
```
default._bimi.postmta.com IN TXT "v=BIMI1; l=https://postmta.com/logo.svg; a=https://postmta.com/bimi.pem"
```

---

## Common Authentication Failures and How to Fix Them

| Error | Cause | Fix |
|-------|-------|-----|
| `dkim=fail` | Signature not added or key mismatch | Check KumoMTA DKIM config, verify DNS TXT record |
| `spf=fail` | Sending IP not in SPF record | Add IP to SPF record |
| `dmarc=fail` | Neither SPF nor DKIM aligned | Fix alignment, check MAIL FROM domain |
| `dmarc=pass` but mail goes to spam | Reputation issue, not authentication | Check IP reputation, content signals |
| `dkim=neutral` | DKIM signature not attempted | KumoMTA not signing, check `dkim.sign=true` |

---

## Authentication Checklist

Before sending any marketing or transactional campaign:

- [ ] SPF record published and includes all sending IPs
- [ ] DKIM keys generated and configured in KumoMTA
- [ ] DKIM public key published in DNS with correct selector
- [ ] DMARC record published starting with `p=none`
- [ ] DMARC aggregate reports going to a monitored inbox
- [ ] DMARC reports reviewed — all legitimate sources passing
- [ ] DMARC progressive rollout planned (none → quarantine → reject)
- [ ] Reverse DNS (PTR) matches your HELO hostname
- [ ] BIMI planned for brand protection

---

## Tools for Email Authentication

- **MXToolbox** — SPF, DKIM, DMARC lookup and DNS check
- **Kitterman SPF Validator** — SPF record syntax checker
- **DMARC Inspector** — DNS and record validation
- **Google Admin Toolbox** — Check SPF/DKIM/DMARC for any domain
- **Mail-Tester** — Send a test email and get full authentication report
- **dmarcian** — DMARC report aggregation and analysis
- **Gmail Postmaster Tools** — Free authentication data for Gmail senders

---

**Related Guides:**
- [KumoMTA Setup Guide](/kumomta-setup-guide) — Configure KumoMTA with proper authentication
- [IP Warmup Best Practices](/ip-warmup-guide) — Build sender reputation alongside authentication
- [Bounce Rate Reduction Guide](/bounce-rate-reduction-guide) — Keep your list clean so authentication matters


*This article was originally published on [PostMTA](https://postmta.com/).*
