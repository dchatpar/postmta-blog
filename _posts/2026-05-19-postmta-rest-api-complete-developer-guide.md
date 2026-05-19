---
title: "PostMTA REST API: Complete Developer Guide"
date: 2026-05-19
tags: ["email", "smtp", "devops", "postmta"]
---

## PostMTA REST API Reference

### Base URL
```plaintext
https://api.postmta.com/v1
```

### Authentication
```bash
curl -H "Authorization: Bearer YOUR_API_KEY" \
     https://api.postmta.com/v1/status
```

### Send Email
```bash
POST /v1/send
{
  "from": "marketing@domain.com",
  "to": ["user@example.com"],
  "subject": "Hello",
  "html": "<p>Your message</p>"
}
```

### Endpoints
- `GET /v1/messages/{id}/status`
- `GET /v1/suppressions`
- `POST /v1/suppressions/remove`
- `POST /v1/webhooks`

**[API Docs →](https://postmta.com/docs)**

*[Learn more about PostMTA](https://postmta.com)*