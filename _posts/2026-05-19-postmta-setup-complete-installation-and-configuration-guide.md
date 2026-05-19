---
title: "PostMTA Setup: Complete Installation and Configuration Guide"
date: 2026-05-19
tags: ["email", "smtp", "devops", "postmta"]
---

Get PostMTA running in 15 minutes.

## Prerequisites

Linux server (Ubuntu 20.04+ or CentOS 8+), 2GB RAM minimum, dedicated IP, domain with DNS access.

## Installation

curl -sSL https://apt.postmta.com/ | sudo bash
sudo apt-get install postmta
sudo postmta init --domain yourdomain.com

## DNS Setup

Add MX record, set up SPF, generate DKIM keys, configure DMARC.

## First Send

postmta send --to user@example.com --subject Test --body Hello

Full guide at postmta.com/docs

*[Learn more about PostMTA](https://postmta.com)*