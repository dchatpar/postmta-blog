---
title: "Building Email Sender Reputation from Scratch: A No-Nonsense Guide"
date: 2026-05-19
tags: ["email", "smtp", "devops", "postmta"]
---

Starting to send email from new infrastructure? Your reputation starts at zero. Here's how to build it correctly.

**Sender Reputation Components**
1. IP reputation (which IPs you're sending from)
2. Domain reputation (your from domain, return path domain)
3. Content reputation (what you're sending)

**Phase 1: Foundation (Days 1-30)**
- Set up proper authentication (SPF, DKIM, DMARC)
- Use dedicated IPs, not shared pools
- Send only to highly engaged recipients (opened in last 30 days)
- Keep volume under 1,000/day
- Use consistent sending schedule

**Phase 2: Establishing (Days 31-60)**
- Gradually increase volume 20% weekly
- Introduce moderately engaged contacts
- Monitor complaint rates obsessively
- Keep bounce rate under 2%

**Phase 3: Scaling (Days 61-90)**
- Increase to normal campaign volumes
- Add less engaged segments carefully
- Expand to new domains gradually
- Your reputation score should now be "neutral" to "positive"

**What Destroys Reputation Fast**
- Buying or scraping lists
- Ignoring bounces
- Sending to people who didn't request your emails
- Sudden volume spikes
- Content that triggers spam filters

**PostMTA's Reputation Tools**
Automatic warmup scheduling, bounce rate alerts, ISP reputation monitoring — all built into the platform.

Start building: https://postmta.com

#email #reputation #postmta #deliverability

*[Learn more about PostMTA](https://postmta.com)*