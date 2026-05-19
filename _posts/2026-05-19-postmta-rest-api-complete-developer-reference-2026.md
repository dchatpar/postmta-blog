---
title: "PostMTA REST API: Complete Developer Reference 2026"
date: 2026-05-19
tags: ["email", "smtp", "devops", "postmta"]
---

Complete PostMTA API reference for developers.

## Core Endpoints

POST /v1/send - Send email
GET /v1/status/{message_id} - Check delivery status
POST /v1/webhooks - Register webhooks
GET /v1/analytics - Get metrics
POST /v1/suppression/{type} - Manage suppressions
GET /v1/ips - List dedicated IPs

All API calls require Bearer token. Generate tokens in dashboard.

Full reference: postmta.com/api

*[Learn more about PostMTA](https://postmta.com)*