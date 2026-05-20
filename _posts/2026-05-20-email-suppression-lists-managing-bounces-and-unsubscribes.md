---
title: "Email Suppression Lists: Managing Bounces and Unsubscribes"
date: 2026-05-20
tags: ["email", "smtp", "devops", "postmta"]
---

Suppression lists protect sender reputation by preventing sends to addresses that have bounced, complained, or unsubscribed.

## Types of Suppressions

Hard Bounce: Invalid address. Never try again.
Soft Bounce: Temporary failure. Retry with backoff.
Complaint: User marked as spam. Remove immediately.
Unsubscribe: Explicit opt-out. Honor immediately.

## PostMTA Features

Real-time sync, configurable rules, import/export, API management, automatic FBL suppression.

PostMTA automates all of this. postmta.com

*[Learn more about PostMTA](https://postmta.com)*