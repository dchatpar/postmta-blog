---
layout: post
title: "IP Warmup Guide: Build Sender Reputation from Zero"
date: 2026-05-18
categories: ["ip-warmup", "deliverability"]
---

# IP Warmup Guide: Build Sender Reputation from Zero

Fresh IP addresses have no reputation. Sending volume suddenly on a new IP is the fastest way to end up in spam folders. This guide covers how to warm up properly.

## Why IP Warmup Matters

Internet service providers (Gmail, Outlook, Yahoo) track sender reputation using:
- IP address history
- Sending volume patterns
- Complaint rates
- Bounce rates

Starting cold means starting at zero reputation. Ramping gradually builds trust.

## The PostMTA Warmup Schedule

PostMTA's IP warmup automation follows industry best practices:

| Day | Volume |
|-----|--------|
| 1-3 | 100/day |
| 4-7 | 500/day |
| 8-14 | 2,000/day |
| 15-21 | 10,000/day |
| 22-30 | 50,000/day |
| 31+ | Full volume |

## Automation with PostMTA

Enable warmup mode on new IPs:

```bash
curl -X POST https://api.postmta.com/v1/ips/warmup \
  -H "Authorization: Bearer KEY" \
  -d '{"ip": "45.33.97.220", "target_volume": 100000}'
```

PostMTA automatically:
- Monitors delivery rates
- Adjusts volume if blocks detected
- Shifts traffic to warm IPs
- Reports warmup progress daily

## Common Mistakes

- ❌ Starting at 50%+ of target volume
- ❌ Not monitoring complaint rates
- ❌ Warming all IPs simultaneously
- ✅ Start slow, monitor daily
- ✅ Use dedicated IPs for transactional vs marketing

**[Set up IP warmup with PostMTA →](https://postmta.com)**
