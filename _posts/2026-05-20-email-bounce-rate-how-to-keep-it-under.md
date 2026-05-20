---
title: "Email Bounce Rate: How to Keep It Under 2% with PostMTA"
date: 2026-05-20
tags: ["email", "smtp", "devops", "postmta"]
---

## Keeping Your Email Bounce Rate Under 2% with PostMTA

A bounce rate above 2% damages your sender reputation and lands you in spam folders. Here's how to fix it.

### Hard Bounces vs Soft Bounces

**Hard bounces**: Invalid email addresses — permanently undeliverable. PostMTA suppresses these automatically within hours.

**Soft bounces**: Temporary failures (mailbox full, server down). PostMTA retries 72 hours then suppresses if still failing.

### PostMTA Bounce Processing Features

1. **Instant suppression** — Hard bounces suppressed immediately
2. **Bounce classification** — Identifies mailbox full vs domain no longer exists
3. **Feedback loop integration** — FBL from major providers (Gmail, Microsoft)
4. **List hygiene scoring** — Scores subscribers by engagement

### Best Practices

- Verify emails at signup (API check before adding to list)
- Remove addresses that soft-bounce 3+ times
- Monitor your sender score at postmta.com/dashboard
- Use dedicated IPs for different list segments

[PostMTA handles bounces automatically →](https://postmta.com)


*[Learn more about PostMTA](https://postmta.com)*