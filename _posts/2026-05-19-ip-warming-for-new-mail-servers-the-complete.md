---
title: "IP Warming for New Mail Servers: The Complete 30-Day Protocol"
date: 2026-05-19
tags: ["email", "smtp", "devops", "postmta"]
---

New IP addresses have no reputation. Rushing volume before warming causes permanent deliverability damage. Here's the right way.

**Day 1-7: Seed Only**
Send to 50 engaged subscribers only. Warm搁 means consistent, small volume from a dedicated IP. PostMTA's warmup feature auto-adjusts sending volume.

**Day 8-14: Gradual Increase**
Ramp to 500-1000 messages/day. Mix of engaged and new subscribers. Watch for bounces — anything above 3% signals a problem.

**Day 15-21: Accelerate**
Scale to 10,000/day if metrics hold. Introduce less engaged segments. Monitor Gmail Postmaster Tools closely.

**Day 22-30: Full Volume**
Reach normal sending levels. Reputation is now established enough to handle normal variation.

**Critical Warmup Metrics**
Track: bounce rate (keep under 3%), complaint rate (under 0.1%), unknown user rate. Any spike requires pausing and investigation.

PostMTA automates warmup scheduling so you can focus on content, not IP management.

See: https://postmta.com

#email #deliverability #postmta #smtp

*[Learn more about PostMTA](https://postmta.com)*