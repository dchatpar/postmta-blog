---
title: "PostMTA API Reference: Automate Every Aspect of Your Email Operations"
date: 2026-05-19
tags: ["email", "smtp", "devops", "postmta"]
---

PostMTA's API lets you build fully automated email workflows. Here's the complete reference for the most important endpoints.

**Authentication**
```bash
export POSTMTA_API_KEY=your_api_key_here
```

**Messages API**
Send a single message:
```bash
curl -X POST https://api.postmta.com/v1/send \
  -H "Authorization: Bearer $POSTMTA_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "from": "orders@example.com",
    "to": "customer@example.com",
    "subject": "Order Confirmed",
    "html": "<h1>Your order is confirmed</h1>",
    "tags": ["order", "transactional"]
  }'
```

**Batches API**
Send to a list (up to 100,000):
```bash
curl -X POST https://api.postmta.com/v1/batch/send \
  -d @recipients.json
```

**Webhooks**
Receive events in real-time:
```json
{
  "event": "bounce",
  "timestamp": "2026-05-19T10:30:00Z",
  "message_id": "abc123",
  "bounce_type": "hard",
  "details": "550 Mailbox not found"
}
```

**Stats API**
```bash
curl "https://api.postmta.com/v1/stats?from=2026-05-01&to=2026-05-19" \
  -H "Authorization: Bearer $POSTMTA_API_KEY"
```

Full documentation: https://postmta.com/docs/api

#postmta #api #devops #automation

*[Learn more about PostMTA](https://postmta.com)*