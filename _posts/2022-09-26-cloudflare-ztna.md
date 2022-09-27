---
layout: post
title: "ZTNA to Services Through Cloudflare"
date: 2022-09-25 09:00:00 -0500
categories: homelab
tags: homelab cloudflare zero trust ztna warp sase
---

## Overview

If the vendor marketing is any indication, "Zero Trust" is the biggest buzzword in security as of late. But not without reason; a well implemented security architecture with strong identity verification and zero trust principles is the foundation of good security today. It's amsuing to me as of late, how many breaches you hear the same things; valid account, compromise of phishable 2-factor credentials, compromise of enterprise VPN, establish foothold, move laterally.

As Cloudflare puts as they "eat their own dog food" when it comes to solutions, for me this is adopting a zero trust approach to self hosted services. For a while, I have been using Cloudflare Tunnels (formerly known as Argo Tunnels) with identity providers as sort-of a "front line" identity verification to services. This is cool and all, but it doesn't fully automate the identification process, and breaks a lot of "app" functionality, ie, when using Home Assistant or Bitwarden.

In this post, I'll assume you already have apps configured with public hostnames configured via Cloudflare Tunnels, and perhaps some policies to govern access already. What I want to highlight is the addition of "gateway" access by configuration of managed devices within a team/organization through the "Warp" client.

## Initial Setup

- Warp Client Configuration
- Device Enrollment Policy
- Gateway Configuration

## Device Enrollment

- iOS App Installation
- Login to app

## Certificate Trust

- Installation & trust of cloudflare certificate

## Policy Configuration

- Creation of a gateway authentication bypass policy

## Word of Warning

- Understanding TLS interception, cert pinning, and other issues

![do-not-inspect](/assets/img/do-not-inspect.png)

## References

⚙️ Link 1

⚙️ Link 2
