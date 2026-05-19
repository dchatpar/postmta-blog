---
title: "SPF DKIM DMARC: Complete Email Authentication Explained"
date: 2026-05-19
tags: ["email", "smtp", "devops", "postmta"]
---

Email authentication prevents spoofing and improves deliverability.

## SPF (Sender Policy Framework)

Tells receiving servers which servers can send mail for your domain.
v=spf1 ip4:x.x.x.x include:_spf.postmta.com ~all

## DKIM (DomainKeys Identified Mail)

Cryptographic signature proving email was not altered. PostMTA auto-generates DKIM keys and signs all outbound mail.

## DMARC (Domain-based Message Authentication)

Policy telling receivers what to do with unauthenticated mail.
p=none: Monitor only
p=quarantine: Mark suspicious as spam
p=reject: Block suspicious mail entirely

PostMTA sets up SPF, DKIM, DMARC automatically. postmta.com

*[Learn more about PostMTA](https://postmta.com)*