---
layout: post
title: "How to Set Up KumoMTA: A Complete Guide for High-Volume Email Senders"
date: 2026-05-18
permalink: /:title/
description: "title: "How to Set Up KumoMTA: A Complete Guide for High-Volume Email Senders" date: "2026-05-18" tags: ["KumoMTA", "Email Setup", "MTA", "Email Infrastructure""
tags: [email, MTA, KumoMTA, SMTP, devops]
canonical_url: https://postmta.com/
---

---
title: "How to Set Up KumoMTA: A Complete Guide for High-Volume Email Senders"
date: "2026-05-18"
tags: ["KumoMTA", "Email Setup", "MTA", "Email Infrastructure", "Docker", "Kubernetes"]
excerpt: "A step-by-step technical guide to deploying KumoMTA for high-volume email delivery — from Docker installation to DKIM/DMARC setup and IP warmup."
---


KumoMTA's combination of open-source licensing and modern architecture makes it an attractive choice for teams ready to move beyond legacy MTA solutions. This guide walks you through a production-ready KumoMTA deployment — from first install to warm IP and monitoring.

---

## Prerequisites

Before installing KumoMTA, ensure you have:

- **Linux server** (Ubuntu 22.04+ or RHEL 9+ recommended)
- **Docker** (for containerized deployment) or **kubectl** (for Kubernetes)
- **Domain names** with DNS access for MX, SPF, DKIM, and DMARC records
- **Dedicated IP addresses** (at least 2 for warmup rotation)
- **PostgreSQL or SQLite** for delivery tracking (optional but recommended)
- **Prometheus + Grafana** for metrics (optional but strongly recommended)
- **Root or sudo access**

---

## Installation Methods

### Option 1: Docker (Recommended for Most Teams)

```bash
docker pull ghcr.io/prozesshell/kumomta:latest

mkdir -p /opt/kumomta/{config,data,log}

docker run -d \
  --name kumomta \
  -p 25:25 \
  -p 587:587 \
  -p 465:465 \
  -v /opt/kumomta/config:/etc/kumomta \
  -v /opt/kumomta/data:/var/lib/kumomta \
  -v /opt/kumomta/log:/var/log/kumomta \
  ghcr.io/prozesshell/kumomta:latest
```

### Option 2: Kubernetes with Helm

```bash
helm repo add kumomta https://charts.kumomta.com
helm repo update

helm install kumomta kumomta/kumomta \
  --set replicaCount=3 \
  --set config.mail.tls.enabled=true \
  --set resources.requests.cpu=500m \
  --set resources.requests.memory=1Gi
```

---

## Basic Configuration

KumoMTA's main configuration file lives at `/etc/kumomta/kumomta.conf`. Here's a production-ready baseline:

```lua
-- KumoMTA Configuration
kumo.start_server()

-- SMTP Listener
kumo:define_smtp_listener({
  listen = '[::]:25',
  relay_hosts = { '127.0.0.1' },
  -- Allow authenticated relays
  submission = true,
})

-- DKIM Signing
kumo:define_dkim_signer({
  domain = 'yourdomain.com',
  selector = 'mail',
  key_path = '/etc/kumomta/keys/dkim.pem',
  headers = { 'From', 'To', 'Subject' },
})

-- Traffic Shaping (per tenant)
kumo:define_traffic_shaper({
  name = 'default',
  max_message_rate = 1000,  -- per second
  max_connection_rate = 100,
  max_outbound_connections = 1000,
})

-- Prometheus Metrics
kumo:define_source({
  name = 'prometheus',
  protocol = 'prometheus',
  listen = '[::]:8000',
})

-- Logging
kumo:define_log({
  path = '/var/log/kumomta/smtp.log',
  level = 'info',
})
```

After saving, validate and reload:

```bash
kumomta config validate /etc/kumomta/kumomta.conf
kumomta reload
```

---

## DKIM and DMARC Setup

### Generate DKIM Keys

```bash
openssl genrsa -out /etc/kumomta/keys/dkim.pem 2048
openssl rsa -in /etc/kumomta/keys/dkim.pem -pubout > /etc/kumomta/keys/dkim.pub
chmod 600 /etc/kumomta/keys/dkim.pem
```

### DNS Records

Add these records in your DNS provider:

**DKIM Record** (TXT record at `mail._domainkey.yourdomain.com`):
```
v=DKIM1; k=rsa; p=YOUR_PUBLIC_KEY_HERE
```

**SPF Record** (TXT at your domain root):
```
v=SPF1 include:_spf.yourdomain.com ~all
```

**DMARC Record** (TXT at `_dmarc.yourdomain.com`):
```
v=DMARC1; p=quarantine; rua=mailto:dmarc@yourdomain.com; pct=100
```

---

## IP Warmup Strategy

Never send high volume from a cold IP. Use this rotation schedule:

| Week | Daily Volume Cap | Notes |
|---|---|---|
| 1 | 1,000 emails/day | Warmup phase — monitor bounces |
| 2 | 10,000 emails/day | Watch complaint rates |
| 3 | 50,000 emails/day | Check inbox placement |
| 4 | 200,000 emails/day | Observe reputation |
| 5+ | Scale as reputation builds | Add second IP, repeat |

KumoMTA's multi-tenant traffic shaping makes rotating warmup easy — assign each tenant a specific IP pool and let the shaping policies enforce the warmup schedule.

---

## Monitoring with Prometheus and Grafana

KumoMTA exposes metrics at `http://yourserver:8000/metrics`. Add this to your Prometheus config:

```yaml
scrape_configs:
  - job_name: 'kumomta'
    static_configs:
      - targets: ['your-kumomta-host:8000']
```

Key metrics to watch:
- `kumomta_smtp_messages_total` — total messages processed
- `kumomta_smtp_delivery_latency_seconds` — delivery latency histogram
- `kumomta_smtp_bounce_rate` — bounce percentage by type
- `kumomta_tls_connections_total` — TLS vs plaintext ratio

Import the official KumoMTA Grafana dashboard (ID: `19876`) for instant visibility.

---

## Common Pitfalls

1. **Skipping IP warmup** — Cold IPs get blacklisted fast. Follow the rotation schedule strictly.
2. **Missing DKIM keys** — Without DKIM, Gmail and Outlook will junk your mail.
3. **No DMARC monitoring** — You won't know you're failing authentication until inbox placement drops.
4. **Insufficient connection limits** — KumoMTA's default limits are conservative; tune them for your volume.
5. **Ignoring bounce codes** — Hard bounces damage reputation; process them within hours, not days.

---

## Conclusion

KumoMTA's modern architecture, Lua configuration flexibility, and AI-assisted deployment make it a powerful choice for high-volume senders ready to leave legacy MTA solutions behind.

Getting it right the first time matters — misconfigured DKIM, inadequate warmup, or missing monitoring will cost you inbox placement that takes months to rebuild.

**Need a production-ready KumoMTA deployment without the guesswork?** PostMTA's engineering team specializes in KumoMTA setup, IP warmup, and deliverability optimization. We'll have you sending at full volume within weeks, not months.

👉 **[Get a free KumoMTA setup consultation →](https://postmta.com/contact)**


*This article was originally published on [PostMTA](https://postmta.com/).*
