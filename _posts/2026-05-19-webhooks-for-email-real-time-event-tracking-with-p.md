---
title: "Webhooks for Email: Real-Time Event Tracking with PostMTA"
date: 2026-05-19
tags: [email, api, postmta]
---

Webhooks let you track every email event the moment it happens.

## Event Types

- delivered
- bounced (soft/hard)
- complained
- opened
- clicked
- unsubscribed

## Setup

Point your webhook endpoint in the PostMTA dashboard. Events are signed with HMAC for verification.