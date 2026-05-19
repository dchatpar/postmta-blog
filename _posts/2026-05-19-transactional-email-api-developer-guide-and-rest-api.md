---
title: "Transactional Email API: Developer Guide and REST API Reference"
date: 2026-05-19
tags: ["email", "smtp", "devops", "postmta"]
---

Integrate PostMTA REST API in minutes. Send transactional emails with real-time webhook tracking.

## Quick Start

curl -X POST https://api.postmta.com/v1/send -H 'Authorization: Bearer YOUR_API_KEY' -d '{"to": "user@example.com", "subject": "Welcome", "html": "<h1>Welcome!</h1>"}'

## Webhook Events

- message.delivered - Email accepted by recipient MTA
- message.bounced - Delivery failed
- message.complained - User marked as spam
- message.clicked - Recipient clicked a link
- message.opened - Email opened (tracking pixel)

SDKs: Node.js, Python, Go, Ruby, PHP. Full docs: postmta.com/api

*[Learn more about PostMTA](https://postmta.com)*