---
layout: post
title: "IP Warmup for New Mail Transfer Agents: A Technical Strategy Guide"
date: 2026-05-18
permalink: /:title/
description: "title: "IP Warmup for New Mail Transfer Agents: A Technical Strategy Guide" date: "2026-05-18" tags: ["IP Warmup", "Email Deliverability", "MTA", "Email Infrast"
tags: [email, MTA, KumoMTA, SMTP, devops]
canonical_url: https://postmta.com/
---

---
title: "IP Warmup for New Mail Transfer Agents: A Technical Strategy Guide"
date: "2026-05-18"
tags: ["IP Warmup", "Email Deliverability", "MTA", "Email Infrastructure", "Sender Reputation"]
excerpt: "Why IP warmup is critical for new MTA deployments, and the exact strategy to build sender reputation from zero to full volume without blacklists."
---


You just deployed your new KumoMTA or PowerMTA server. You have clean IPs, perfect DNS, and a million emails queued to send. **Stop.** If you send that volume immediately, you'll be blacklisted within 48 hours. This guide explains why IP warmup matters and exactly how to do it right.

---

## Why IP Warmup Is Non-Negotiable

Internet service providers (Gmail, Outlook, Yahoo, Apple Mail) use sender reputation as their primary spam filter. Reputation is a score between -10 and 10 attached to your sending IP address, built from:

- **Volume history** — How much email you send and how consistently
- **Bounce rate** — Percentage of invalid recipients
- **Complaint rate** — How many recipients mark you as spam
- **Spam trap hits** — Emails sent to dormant/harvested addresses
- **Engagement** — Read, click, reply rates on your messages

A brand new IP has **zero reputation**. ISPs treat zero-reputation IPs as high-risk by default. Sending high volume from a cold IP is the fastest way to signal to every major ISP that you're a spammer.

---

## The 8-Week IP Warmup Schedule

This schedule works for any major ISP. Adjust based on your bounce and complaint data.

### Phase 1: Verification (Days 1–7)

Before sending anything, verify your infrastructure:

- [ ] SPF, DKIM, and DMARC DNS records are live and passing
- [ ] Your From domain matches your sending domain
- [ ] List is double opt-in — zero purchased or scraped lists
- [ ] Postmaster tools registered: [Gmail Postmaster](https://postmaster.google.com), [Microsoft SNDS](https://sendersupport.olc.protection.outlook.com/snds/)
- [ ] Dedicated IPs assigned (minimum 2 for rotation)

### Phase 2: Soft Launch (Days 8–14)

**Day 8–10: 500 emails/day**
- Send to your most engaged subscribers only (opened in last 30 days)
- No HTML formatting — plain text preferred
- No attachments, no images
- Watch bounce rate: must stay below 2%

**Day 11–14: 2,000 emails/day**
- Expand to 60-day engaged segment
- Include simple HTML (logo + text)
- Track complaint rate: must stay below 0.1%

### Phase 3: Ramp (Days 15–28)

| Day | Volume |
|---|---|
| 15–17 | 10,000/day |
| 18–20 | 25,000/day |
| 21–24 | 75,000/day |
| 25–28 | 150,000/day |

### Phase 4: Scale (Days 29–56)

| Week | Volume |
|---|---|
| 5 | 300,000/day |
| 6 | 600,000/day |
| 7 | 1,000,000/day |
| 8+ | Full volume |

**Golden rule: Never increase by more than 2–3x week-over-week once above 50K/day.** Faster ramps trigger ISP throttling.

---

## MTA-Level Warmup Controls

### KumoMTA: Traffic Shaper Warmup

Use Lua scripting to enforce warmup caps automatically:

```lua
-- Warmup traffic shaper per IP pool
kumo:define_traffic_shaper({
  name = 'warmup-phase-1',
  max_message_rate = 50,    -- ~5,000/day assuming 8h active
  max_outbound_connections = 10,
})

kumo:define_traffic_shaper({
  name = 'warmup-phase-3',
  max_message_rate = 500,
  max_outbound_connections = 50,
})

-- Assign senders to warmup pools
kumo:define_smtp_source({
  name = 'warmup-sender-1',
  shaper = 'warmup-phase-1',
  egress_pool = 'warmup-pool-1',
})
```

### PowerMTA: Per-IP Rate Limiting

```
domain IP-address-pool warmup-pool-1
  ip-pool warmup-pool-1
    max-msg-rate 50/m
    max-rcpt-per-msg 1
    max-smtp-out 10
```

Rotate IPs through progressively larger pools as reputation builds.

---

## Monitoring Dashboard

Track these metrics daily during warmup:

| Metric | Good | Warning | Danger |
|---|---|---|---|
| Bounce rate | < 2% | 2–5% | > 5% |
| Complaint rate | < 0.1% | 0.1–0.5% | > 0.5% |
| Spam trap hits | 0 | 1–2/week | 3+/week |
| Inbox placement | > 95% | 85–95% | < 85% |

Set up [Gmail Postmaster Tools](https://postmaster.google.com) and [Microsoft SNDS](https://sendersupport.olc.protection.outlook.com/snds/) alerts for reputation drops.

---

## What Breaks Warmup (And How to Recover)

### Problem: Bounce Spike
**Cause:** Sending to invalid addresses — likely a dirty list
**Fix:** Pause sends immediately. Scrub your list against email verification API. Resume at 50% of previous volume.

### Problem: Complaint Spike
**Cause:** Recipients don't remember opting in, or subject lines are misleading
**Fix:** Review your subject lines. Re-engage dormant subscribers with a confirmation email before resuming.

### Problem: Blacklisted by Spamhaus
**Cause:** Sending to spam traps or very high bounce rates
**Fix:** Remove hard bounces immediately. Submit delist request at [spamhaus.org](https://spamhaus.org/lookup/). Wait 24–48h before resuming.

### Problem: Gmail Promotions Tab
**Cause:** Normal for marketing email; not a deliverability problem
**Fix:** Gmail's Promotions tab is not a blacklist. Focus on open rates and click rates regardless of tab placement.

---

## Conclusion

IP warmup is not optional — it's the foundation of your sender reputation. A proper 8-week warmup gives you inbox placement that lasts years. Rush it and you'll spend months recovering from blacklists and ISP throttling.

The investment is in the setup: proper traffic shaping, monitoring dashboards, and a clear rollback plan. With KumoMTA's Lua scripting or PowerMTA's IP pools, warmup can be automated and hands-off after the initial configuration.

**Need help designing your IP warmup strategy?** PostMTA's deliverability engineers have warmed hundreds of IP ranges across Gmail, Outlook, Yahoo, and regional ISPs. We'll build your warmup playbook, configure your MTA, and monitor your first 90 days until you're hitting full volume with 98%+ inbox rates.

👉 **[Talk to a deliverability specialist →](https://postmta.com/contact)**


*This article was originally published on [PostMTA](https://postmta.com/).*
