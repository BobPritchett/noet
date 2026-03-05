# noet.com — Static Site Deployment

This repository contains the source for **[https://noet.com](https://noet.com)**, a simple static website deployed automatically via **Cloudflare Pages** and built from this GitHub repository.

The deployment pipeline is intentionally minimal: there is **no build step**, no framework, and the site is served as static files directly from Cloudflare’s global CDN.

---

# Architecture Overview

The site is deployed using the following pipeline:

```
GitHub repository (this repo)
        ↓
Cloudflare Pages build system
        ↓
Preview domain
https://noet.pages.dev
        ↓
Production domain
https://noet.com
```

Cloudflare automatically rebuilds and redeploys the site whenever changes are pushed to the `main` branch.

---

# Deployment Platform

The site is hosted on **Cloudflare Pages**, part of the Cloudflare edge platform.

Key characteristics:

- Global CDN delivery
- Automatic HTTPS
- Automatic deployments from GitHub
- Zero build step (static file hosting)
- Instant rollbacks via deployment history

---

# GitHub Integration

Repository:

```
https://github.com/BobPritchett/noet
```

Cloudflare Pages is configured to:

- watch the `main` branch
- clone the repository on each push
- deploy static files directly

Build settings:

```
Framework preset: None
Build command: (none)
Build output directory: /
```

Because the site is static HTML/CSS/JS, no build step is required.

---

# DNS Configuration

DNS for `noet.com` is managed through **Cloudflare DNS**.

The key records are:

```
CNAME  noet.com   →  noet.pages.dev   (proxied)
CNAME  www        →  noet.com         (proxied)
```

This configuration means:

```
noet.com        → Cloudflare Pages deployment
www.noet.com    → redirected through noet.com
```

Cloudflare's **CNAME flattening** allows the apex domain (`noet.com`) to point to the Pages domain.

Additional DNS records support email via Microsoft 365:

```
MX     noet.com          → Microsoft mail servers
TXT    SPF               → include:spf.protection.outlook.com
TXT    _dmarc            → DMARC policy
CNAME  selector1._domainkey → DKIM
CNAME  selector2._domainkey → DKIM
```

These records are **DNS-only (not proxied)** to ensure proper email delivery.

---

# Deployment Process

Deployment happens automatically when code is pushed.

Typical workflow:

```
git add .
git commit -m "Update site"
git push origin main
```

Cloudflare Pages then:

1. Clones the repository
2. Skips build (no build command configured)
3. Uploads static assets
4. Publishes them to the global CDN

Typical log output:

```
Cloning repository
No build command specified. Skipping build step.
Uploading assets
Success: Assets published
```

Deployments normally complete in **under one minute**.

---

# Preview Deployments

Each deployment generates a preview URL.

Example:

```
https://<hash>.noet.pages.dev
```

This allows testing before the production alias (`noet.pages.dev`) updates.

---

# Recommended Static Configuration Files

The repository may optionally include these Cloudflare Pages configuration files.

## `_headers`

Controls caching and security headers.

Example:

```
/*
  X-Frame-Options: DENY
  X-Content-Type-Options: nosniff
  Referrer-Policy: strict-origin-when-cross-origin
  Cache-Control: public, max-age=3600
```

---

## `_redirects`

Defines redirects and rewrites.

Example:

```
/index.html /
```

Or for single-page apps:

```
/* /index.html 200
```

---

## `robots.txt`

Guides search engine crawling.

Example:

```
User-agent: *
Allow: /
```

---

# Local Development

No special tooling is required.

The site can be edited locally and opened directly in a browser.

Example:

```
index.html
styles.css
images/
```

To preview locally:

```
open index.html
```

or use a simple static server if desired.

---

# Deployment Status

Production deployment:

```
https://noet.com
```

Cloudflare Pages preview domain:

```
https://noet.pages.dev
```

---

# Notes

This project intentionally avoids frameworks or build systems to keep the deployment simple and extremely fast.

Cloudflare’s edge CDN serves the site globally with minimal infrastructure.

---

# Maintainer

Repository owner:

Bob Pritchett
