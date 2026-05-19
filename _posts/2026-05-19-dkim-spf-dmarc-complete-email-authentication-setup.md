---
title: "DKIM SPF DMARC: Complete Email Authentication Setup"
date: 2026-05-19
tags: [email, api, postmta]
---

Email authentication is non-negotiable for deliverability. Here's how to configure DKIM, SPF, and DMARC for PostMTA.

## SPF

Add to your DNS: `v=spf1 include:postmta.com ~all`

## DKIM

PostMTA generates DKIM keys automatically. Add the public key to your DNS with selector `postmta._domainkey`.

## DMARC

Start with: `v=DMARC1; p=quarantine; rua=mailto:reports@postmta.com`

## Why It Matters

Without these, Gmail and Yahoo will block your emails regardless of content quality.