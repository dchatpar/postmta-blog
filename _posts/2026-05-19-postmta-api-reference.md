---
title: "PostMTA REST API: Complete Reference Guide"
date: 2026-05-19
category: Developer
tags: [api, automation, postmta, smtp]
---

PostMTA exposes a full REST API for programmatic control of your email infrastructure. This reference covers all major endpoints.

## Authentication

All API requests require a Bearer token:

```bash
curl -H "Authorization: Bearer YOUR_API_KEY" \
  https://your-postmta-server.com/api/v1/status
```

Generate API keys in the PostMTA dashboard under Settings > API Keys.

## Core Endpoints

### Queue Statistics

```
GET /api/v1/queue/stats
```

Returns current queue depth, delivery rates, bounce rates, and sending velocity:

```json
{
  "queue_depth": 1247,
  "delivery_rate": 99.2,
  "bounce_rate": 0.8,
  "complaint_rate": 0.01,
  "sending_velocity": 450
}
```

### Send Transactional Email

```
POST /api/v1/send
```

```json
{
  "to": "user@example.com",
  "from": "orders@yourstore.com",
  "subject": "Order Confirmed - #12345",
  "html": "<h1>Thanks for your order!</h1>",
  "headers": {
    "X-Order-ID": "12345",
    "X-User-ID": "u_789"
  }
}
```

### Suppression List Management

```
POST /api/v1/suppression
```

```json
{
  "email": "bounced@example.com",
  "reason": "hard_bounce",
  "source": "feedback_loop"
}
```

```
GET /api/v1/suppression?reason=hard_bounce&limit=1000
```

### IP Management

```
GET /api/v1/ips                    # List all IPs
GET /api/v1/ips/1.2.3.4/warmup-status  # Warmup progress
POST /api/v1/ips/1.2.3.4/pause    # Pause this IP
POST /api/v1/ips/1.2.3.4/resume   # Resume this IP
```

### Domain Management

```
POST /api/v1/domains
```

```json
{
  "domain": "marketing.yourbrand.com",
  "dkim_selector": "mail2",
  "tracking_domain": "t.yourbrand.com"
}
```

## Webhooks

Configure real-time event delivery to your application:

```yaml
webhooks:
  - events: [bounce, complaint, delivery, open, click]
    url: https://yourapp.com/webhooks/postmta
    secret: your_webhook_secret
    retry: 3
```

Webhook payloads:

**Bounce:**
```json
{
  "event": "bounce",
  "email": "user@example.com",
  "type": "hard",
  "smtp_code": 550,
  "timestamp": "2026-05-19T12:00:00Z"
}
```

**Complaint:**
```json
{
  "event": "complaint",
  "email": "user@example.com",
  "provider": "gmail",
  "timestamp": "2026-05-19T12:00:00Z"
}
```

## SDKs

- **Python**: `pip install postmta-sdk`
- **Node.js**: `npm install postmta-api`
- **Go**: `go get github.com/postmta/postmta-go`

Full API documentation: [https://postmta.com/docs/api](https://postmta.com/docs/api)
