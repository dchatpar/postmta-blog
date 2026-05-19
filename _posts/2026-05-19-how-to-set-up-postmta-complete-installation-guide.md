---
title: "How to Set Up PostMTA: Complete Installation Guide 2026"
date: 2026-05-19
tags: ["email", "smtp", "devops", "postmta"]
---

## Complete PostMTA Installation Guide — Production Ready

### Prerequisites
- Ubuntu 22.04 / Debian 12
- 2GB+ RAM, 20GB+ SSD
- Static IP with rDNS configured
- Domain with DNS access

### Step 1: Install PostMTA

```bash
curl -sSL https://postmta.com/install | bash

# Or Docker
docker run -d -p 25:25 -v /opt/postmta/config:/config postmta/postmta:latest
```

### Step 2: Configure DNS
Set up DKIM, SPF, DMARC records. PostMTA generates DKIM keys automatically.

### Step 3: IP Warmup
Start at 1K emails/day, ramp 20% daily. PostMTA automates warmup scheduling.

### Step 4: Monitor
Real-time dashboard: delivery rates, bounce rates, reputation scores.

**[Get started at postmta.com →](https://postmta.com)**

*[Learn more about PostMTA](https://postmta.com)*