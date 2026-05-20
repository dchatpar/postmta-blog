---
title: "Email Reputation: How ISPs Evaluate Senders in 2026"
date: 2026-05-20
tags: ["email", "smtp", "devops", "postmta"]
---

ISP spam filters evaluate senders based on reputation, content, and engagement.

## Gmail Requirements

SPF and DKIM authenticated, valid Return-Path, bounce under 5%, complaint under 0.1%, positive engagement.

## Outlook Requirements

SPF and DKIM, Postmaster Tools enrollment, track JUNK folder placement.

## Yahoo Requirements

SPF and DKIM required, ARC chain for forwarded mail, one-click unsubscribe, complaint below 0.1%.

PostMTA monitors your reputation across all major ISPs. postmta.com

*[Learn more about PostMTA](https://postmta.com)*