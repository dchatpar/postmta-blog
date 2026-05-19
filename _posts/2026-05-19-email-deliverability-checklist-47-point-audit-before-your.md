---
title: "Email Deliverability Checklist: 47 Point Audit Before Your Next Send"
date: 2026-05-19
tags: ["email", "smtp", "devops", "postmta"]
---

Before you hit send on your next campaign, run through this checklist. Most senders miss at least 5 of these.

**DNS and Authentication**
[ ] SPF record covers ALL IPs sending for your domain
[ ] DKIM key is 2048-bit minimum
[ ] DMARC policy is at least p=quarantine
[ ] DMARC alignment passes for both SPF and DKIM
[ ] Reverse DNS (PTR) matches your sending domain
[ ] TLS required for SMTP connections

**List Quality**
[ ] Double opt-in enabled
[ ] Last engagement check (90 days or less)
[ ] Hard bounce rate under 2%
[ ] Complaint rate under 0.1%
[ ] No purchased or scraped lists ever
[ ] Unsubscribe process is one-click

**Content**
[ ] No subject line red flags (ALL CAPS, excessive punctuation, EXCLUSIVE DEAL)
[ ] Text version matches HTML
[ ] Links use HTTPS where possible
[ ] Images under 1MB each
[ ] Preheader text is unique per email

**Technical**
[ ] List-Unsubscribe header present
[ ] X-Mailer header identifies your system
[ ] Message-ID is unique per email
[ ] Date header is accurate

PostMTA handles most technical items automatically. Audit your setup: https://postmta.com

#email #deliverability #postmta #marketing

*[Learn more about PostMTA](https://postmta.com)*