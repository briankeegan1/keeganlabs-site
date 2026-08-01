# DNS Setup Instructions — keeganlabs.net → GitHub Pages

## Overview

The site is hosted on GitHub Pages under `briankeegan1.github.io/keeganlabs-site`.
The `CNAME` file in this repo points the custom domain to `keeganlabs.net`.
The site goes live **only when you apply the DNS records below** in Squarespace (the registrar
and DNS manager for keeganlabs.net). GitHub cannot flip this for you.

DNS propagation takes **minutes to a few hours** after you save. HTTPS (TLS certificate)
is provisioned automatically by GitHub Pages after DNS propagates — no action needed on your end.

---

## Step-by-step: Squarespace DNS panel

1. Log in to **Squarespace** → **Domains** → click **keeganlabs.net** → **DNS Settings**
   (or "Advanced DNS" / "Manage DNS", depending on Squarespace's current UI).

2. **Add four A records** (apex domain — `keeganlabs.net` itself):

   | Type | Name | Value | TTL |
   |------|------|-------|-----|
   | A | @ | 185.199.108.153 | 1 hour |
   | A | @ | 185.199.109.153 | 1 hour |
   | A | @ | 185.199.110.153 | 1 hour |
   | A | @ | 185.199.111.153 | 1 hour |

   These are the four GitHub Pages IP addresses (current as of 2026-08).

3. **Add one CNAME record** (www subdomain):

   | Type | Name | Value | TTL |
   |------|------|-------|-----|
   | CNAME | www | briankeegan1.github.io | 1 hour |

4. **Remove any existing conflicting records** on `@` or `www` before saving (a stale A or
   CNAME pointing elsewhere will prevent the site from resolving).

5. Click **Save** (or equivalent in Squarespace's UI).

---

## Verification

After saving, wait ~15 minutes, then verify with either of these methods:

**Terminal (dig):**
```
dig keeganlabs.net A +short
# Should return the four IPs above

dig www.keeganlabs.net CNAME +short
# Should return briankeegan1.github.io.
```

**Browser:**
- Open `http://keeganlabs.net` — should redirect to `https://keeganlabs.net` and show the site.
- The padlock (HTTPS) may take up to an hour after DNS propagates while GitHub provisions the cert.

---

## HTTPS

GitHub Pages automatically provisions a TLS certificate via Let's Encrypt after DNS propagates.
You do not need to do anything. If HTTPS enforcement is not already enabled in the Pages
settings, go to: **GitHub → briankeegan1/keeganlabs-site → Settings → Pages → Enforce HTTPS**
(check the box once it becomes active).

---

## Troubleshooting

| Symptom | Likely cause | Fix |
|---------|-------------|-----|
| `dig` returns old IPs | DNS not propagated yet | Wait up to 1 hour |
| Site loads as `briankeegan1.github.io/keeganlabs-site` (not apex) | CNAME in repo not deployed | Push is live; check Pages settings |
| Browser shows "not secure" | Cert still provisioning | Wait up to 1 hour after DNS propagates |
| Site loads but HTTPS errors | Old conflicting DNS record | Remove it in Squarespace panel |

---

## Reference

GitHub Pages docs on custom domains:
`https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site`

GitHub Pages IP addresses (A records) — canonical source:
`https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site#configuring-an-apex-domain`
