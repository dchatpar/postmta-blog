---
layout: post
title: "PostMTA Setup Guide: Enterprise Email Delivery in 2026"
date: 2026-05-18
categories: ["setup", "getting-started"]
---

# PostMTA Setup Guide: Enterprise Email Delivery in 2026

Deploying enterprise email delivery infrastructure doesn't need to take weeks. PostMTA's AI-powered deployment gets you sending in hours, not days.

## Prerequisites

- Ubuntu 22.04 LTS or similar Linux distribution
- 4+ CPU cores, 8GB+ RAM for production
- Dedicated IP addresses (we recommend 2-4 fresh IPs)
- Domain with DNS access
- SSL certificates (Let's Encrypt or commercial)

## Installation

PostMTA installs via our deployment wizard:

```bash
curl -sSL https://deploy.postmta.com | bash
```

The installer handles:
- KumoMTA core installation
- Database setup (PostgreSQL)
- Redis for queuing
- SSL certificate provisioning
- Initial DKIM key generation
- Firewall configuration

## Initial Configuration

After installation, configure through the REST API or dashboard:

```bash
# Set your domain
curl -X POST https://api.postmta.com/v1/domains \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -d '{"domain": "yourdomain.com", "dkim_selector": "mail"}'

# Configure bounce handling
curl -X POST https://api.postmta.com/v1/bounce \
  -d '{"action": "retry", "categories": ["hard", "soft"]}'
```

## Verification

Send a test message:

```bash
curl -X POST https://api.postmta.com/v1/send \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -d '{
    "from": "noreply@yourdomain.com",
    "to": "test@example.com",
    "subject": "Test from PostMTA",
    "html": "<h1>Success!</h1>"
  }'
```

## Next Steps

- Configure IP warmup automation
- Set up DKIM/SPF/DMARC records
- Configure bounce and complaint handling
- Enable delivery analytics

**[Get started with PostMTA →](https://postmta.com)**