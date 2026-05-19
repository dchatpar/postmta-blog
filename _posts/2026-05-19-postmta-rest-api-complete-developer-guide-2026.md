---
title: "PostMTA REST API: Complete Developer Guide 2026"
date: 2026-05-19
tags: [email, api, postmta]
---

The PostMTA REST API lets you automate every aspect of your email infrastructure — from sending to analytics.

## Authentication

Generate an API key from your dashboard and include it in the Authorization header.

## Endpoints

- POST /send - Send transactional email
- GET /analytics - Delivery metrics
- POST /bounce-handling - Configure bounce rules
- GET /ip-warmup - Monitor warmup status

## Code Examples

```python
import requests
resp = requests.post('https://api.postmta.com/send',
    headers={'Authorization': 'Bearer YOUR_KEY'},
    json={'to': 'user@example.com', 'subject': 'Hello'})
```