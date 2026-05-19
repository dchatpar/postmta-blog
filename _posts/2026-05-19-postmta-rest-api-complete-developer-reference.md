---
title: "PostMTA REST API: Complete Developer Reference"
date: 2026-05-19
tags: [email, api, postmta]
---

The PostMTA REST API provides programmatic control over every aspect of your email infrastructure.

## Authentication

Generate API keys from your dashboard. Use Bearer token authentication.

## Core Endpoints

- `POST /send` - Send transactional email
- `POST /batch` - Send bulk marketing email
- `GET /analytics` - Delivery and engagement metrics
- `POST /suppression` - Manage bounce/complaint suppression list
- `GET /ip-warmup` - Monitor warmup status
- `POST /webhooks` - Configure event webhooks

## Rate Limits

10,000 requests/minute on Professional. Enterprise has unlimited requests.