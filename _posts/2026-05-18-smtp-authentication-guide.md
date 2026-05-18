---
layout: post
title: "SMTP Authentication Explained: A Technical Deep Dive for Email Engineers"
date: 2026-05-18
permalink: /:title/
description: "Every email delivered on the internet passes through SMTP (Simple Mail Transfer Protocol). Yet most engineers who work with email only know it as "the thing tha"
tags: [email, MTA, KumoMTA, SMTP, devops]
canonical_url: https://postmta.com/
---


Every email delivered on the internet passes through SMTP (Simple Mail Transfer Protocol). Yet most engineers who work with email only know it as "the thing that sends our transactional emails." Understanding SMTP authentication mechanisms is essential for anyone responsible for email deliverability.

---

## The SMTP Basics

SMTP was designed in 1982 — long before authentication was a consideration. The protocol runs on port 25 (server-to-server) or 587 (submission with STARTTLS).

### A Standard SMTP Session

```
$ telnet mail.example.com 25
220 mail.example.com ESMTP Postfix
HELO yourserver.com
250 mail.example.com Hello yourserver.com
MAIL FROM:<sender@example.com>
250 OK
RCPT TO:<recipient@target.com>
250 OK
DATA
354 End data with <CR><LF>.<CR><LF>
Subject: Test

This is the body.
.
250 OK: queued as 12345
QUIT
221 Bye
```

Without authentication, anyone can send mail from any address. That's the problem SMTP authentication solves.

---

## SMTP Authentication Mechanisms

### 1. PLAIN

The simplest authentication mechanism. Sends username and password as a single Base64-encoded string:

```
AUTH PLAIN
334 
dXNlcm5hbWUAVXNlcm5hbWU=c2VjcmV0
334 
AQ==
235 Authentication successful
```

**Format:** `\0username\0password` (each separated by null byte, then Base64 encoded)

**Security:** Only secure over TLS (STARTTLS). Never use PLAIN without encryption.

### 2. LOGIN

Older mechanism, still widely supported. Sends username and password separately:

```
AUTH LOGIN
334 VXNlcm5hbWU6
dXNlcm5hbWU=
334 UGFzc3dvcmQ6
c2VjcmV0
235 Authentication successful
```

**Security:** Same as PLAIN — requires TLS. Base64 encoding is not encryption.

### 3. CRAM-MD5 (Challenge-Response)

More secure — the server sends a challenge, and the client responds with a HMAC-MD5 hash that proves they know the password without transmitting it:

```
AUTH CRAM-MD5
334 PDE2OTUyLjEyMzQ1Njc4OTA=
dXNlcm5hbWUgYTJjMWM2ZjJlNWQwMzk2ZTBmODRhMzBjYzdlMmY5OA==
235 Authentication successful
```

**Security:** Password is never transmitted. Still susceptible to replay attacks without additional protection.

### 4. SCRAM-SHA-256 (Salted Challenge Response Authentication Mechanism)

The modern standard. Defined in RFC 5802, it improves on CRAM-MD5 by:
- Using SHA-256 instead of MD5
- Salting the password hash
- Preventing dictionary attacks
- Providing mutual authentication (server proves to client it knows the password too)

```
AUTH SCRAM-SHA-256
334 czs0Ljc4OTAyMDQ3LjM4OTcwNzEwMDA=
c=biws,r=czs0Ljc4OTAyMDQ3LjM4OTcwNzEwMDA=,p=FsWjXmM9v8Y...
335 
d=splain,r=salt,p=storedkey
335
```

**Security:** Current best practice. Use this whenever both client and server support it.

---

## AUTH mechanisms in Practice

Most modern SMTP servers advertise supported mechanisms in the EHLO response:

```
$ telnet mail.example.com 587
220 mail.example.com ESMTP Postfix
EHLO client.example.com
250-mail.example.com
250-PIPELINING
250-SIZE 10240000
250-ETRN
250-STARTTLS
250-AUTH PLAIN LOGIN CRAM-MD5
250-AUTH PLAIN LOGIN CRAM-MD5
250 8BITMIME
```

The `AUTH PLAIN LOGIN CRAM-MD5` line shows supported mechanisms. Always prefer SCRAM-SHA-256 > CRAM-MD5 > PLAIN > LOGIN in that order.

---

## STARTTLS: Encryption in Transit

SMTP without STARTTLS sends everything — including passwords and email content — in plain text. STARTTLS upgrades an unencrypted connection to an encrypted one:

```
$ telnet mail.example.com 587
220 mail.example.com ESMTP Postfix
EHLO client.example.com
250-STARTTLS
250 AUTH PLAIN LOGIN
AUTH LOGIN
334 VXNlcm5hbWU6
...
```

**Without STARTTLS:** An eavesdropper on the network can read all your emails and steal SMTP credentials.

**With STARTTLS:** The connection is encrypted. However, STARTTLS can be stripped by man-in-the-middle attacks (opportunistic encryption doesn't verify certificates by default).

**Strict TLS (RFC 8460):** Forces encryption and verifies certificates. Required for DMARC alignment in some configurations.

KumoMTA configuration for strict TLS:
```
submission {
  tls = {
    enabled = true
    require = true
    verify = true
  }
}
```

---

## OAuth 2.0 for SMTP (XOAUTH2)

Traditional username/password SMTP auth is increasingly blocked by modern email providers:

- **Gmail/Google Workspace:** App Passwords required for SMTP (going away in 2024-2026), OAuth 2.0 is the replacement
- **Microsoft/Office 365:** Modern authentication (OAuth 2.0) required, basic auth being deprecated

### XOAUTH2 Protocol

Instead of username/password, you use an OAuth 2.0 access token:

```
AUTH XOAUTH2
334 eyJhbGciOiJSUzI1NiJ9...
235 Authentication successful
```

The SASL XOAUTH2 format:
```
user=<email>^Aauth=Bearer <access_token>^A^A
```

### Getting OAuth 2.0 Tokens

For Google Workspace:
1. Create a project in Google Cloud Console
2. Enable the Gmail API
3. Create OAuth 2.0 credentials (Client ID + Client Secret)
4. Use the OAuth 2.0 Playground or your own auth flow to get a refresh token
5. Exchange refresh token for access token

For Microsoft:
1. Register an app in Azure AD
2. Request `https://outlook.office365.com/.default` scope
3. Use MSAL library to get tokens

### KumoMTA OAuth 2.0 Configuration

```
auth {
  oauth2 {
    provider = "google"
    client_id = "your-client-id.apps.googleusercontent.com"
    client_secret = "your-client-secret"
    refresh_token = "your-refresh-token"
  }
}
```

---

## SMTP Authentication for Different Providers

### Gmail/Google Workspace

| Method | Status | Notes |
|--------|--------|-------|
| App Passwords | **Deprecated** | Being phased out, use OAuth 2.0 |
| OAuth 2.0 (XOAUTH2) | ✅ Recommended | Requires Azure/GCP setup |
| Basic AUTH | ⚠️ Limited | Only with 2FA App Passwords |

### Microsoft/Office 365

| Method | Status | Notes |
|--------|--------|-------|
| Modern Auth (OAuth 2.0) | ✅ Required | Basic auth being deprecated |
| SMTP AUTH | ⚠️ Conditional | Must be enabled per mailbox |

### Amazon SES

| Method | Status | Notes |
|--------|--------|-------|
| SMTP credentials | ✅ Standard | Generated in SES console |
| IAM users | ✅ Alternative | More granular permissions |
| OAuth 2.0 | ❌ Not supported | SES uses AWS sig v4 |

### Mailgun

| Method | Status | Notes |
|--------|--------|-------|
| SMTP credentials | ✅ Standard | Generated in domain settings |
| API key | ✅ Preferred | Faster, no SMTP overhead |
| OAuth 2.0 | ❌ Not supported | Use API for sending |

---

## Security Best Practices

**1. Always use TLS (STARTTLS) for SMTP submission:**
```
submission {
  tls = { enabled = true, require = false }
}
```

**2. Rotate SMTP credentials regularly:**
Set a calendar reminder every 90 days to rotate SMTP passwords for any service accounts.

**3. Use dedicated credentials per service:**
Don't share SMTP credentials across applications. If one service is compromised, you can revoke one credential set without affecting others.

**4. Monitor for unauthorized SMTP auth attempts:**
Your mail server logs show every authentication attempt. Set up alerts for:
- Failed authentication attempts (indicates brute force)
- Authentication from unexpected IPs
- Authentication for non-existent users

**5. Use OAuth 2.0 wherever possible:**
It's resistant to credential theft and doesn't require storing passwords.

---

## Testing SMTP Authentication

**Test with OpenSSL:**
```bash
openssl s_client -connect mail.example.com:587 -starttls smtp
EHLO test.com
AUTH PLAIN
```

**Test with swaks (Swiss Army Knife for SMTP):**
```bash
swaks --to recipient@example.com \
  --from sender@example.com \
  --server mail.example.com \
  --auth PLAIN \
  --auth-user username \
  --auth-password password \
  --tls
```

**Test OAuth 2.0:**
Use [OAuth 2.0 Playground](https://developers.google.com/oauthplayground/) for Google, or [Microsoft Identity Platform](https://developer.microsoft.com/en-us/graph/graph-explorer/) for Office 365.

---

## Troubleshooting Authentication Failures

| Error | Cause | Solution |
|-------|-------|----------|
| `535 Authentication credentials invalid` | Wrong password or expired token | Regenerate credentials |
| `535 5.7.3 Authentication mechanism not supported` | Client/server mechanism mismatch | Use supported mechanism from EHLO list |
| `534 5.7.9 Application-specific password required` | Google requiring 2FA + App Password | Set up App Password or switch to OAuth 2.0 |
| `454 4.7.0 TLS required` | Server requires encrypted connection | Add STARTTLS to your SMTP client |
| `530 5.7.0 Authentication required` | AUTH not advertised or required | Check EHLO for AUTH mechanisms |

---

**Related Guides:**
- [Email Authentication: DKIM, SPF & DMARC](/email-authentication-dkim-spf-dmarc) — Complete authentication setup
- [KumoMTA Setup Guide](/kumomta-setup-guide) — Configure SMTP authentication in KumoMTA
- [Open Source Email Infrastructure Guide](/open-source-email-infrastructure-guide) — Self-hosted SMTP architecture


*This article was originally published on [PostMTA](https://postmta.com/).*
