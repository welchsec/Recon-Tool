# Recon-Tool

# 🔍 WelchSec Recon Suite

A free, browser-based reconnaissance toolkit for bug bounty hunters and security researchers. Built on GitHub Pages and powered by a Cloudflare Worker proxy — no installation, no backend, no cost.

**Live Tool → [welchsec.github.io/Recon-Tool](https://welchsec.github.io/Recon-Tool/)**

---

## ⚠️ Legal Disclaimer

> **Only use this tool against targets you own or have explicit written permission to test.**
> Unauthorized scanning may violate computer fraud laws including the Computer Fraud and Abuse Act (CFAA) and equivalent legislation in your jurisdiction.
> Always verify targets are within scope before scanning on bug bounty platforms such as HackerOne, Bugcrowd, or Intigriti.

---

## 🧰 What's Inside

The suite contains three modules accessible from the top navigation bar:

### 🔍 Module 1 — Directory & File Scanner
Probes a target URL for exposed files, directories, and sensitive paths using built-in wordlists.

**12 wordlists you can toggle on/off:**

| Wordlist | What It Finds |
|----------|--------------|
| ADMIN | Admin panels, login pages, dashboards |
| BACKUPS | `.zip`, `.sql`, `.bak`, backup archives |
| CONFIG | `.env`, `web.config`, `settings.php`, `credentials.json` |
| API | Swagger docs, GraphQL endpoints, API versioned paths |
| VCS | `.git/`, `.svn/`, exposed source control directories |
| LOGS | `error.log`, `access.log`, Laravel logs |
| DATABASE | phpMyAdmin, Adminer, `.db`, `.sqlite` files |
| UPLOADS | Upload directories, file managers |
| WORDPRESS | `wp-config.php`, `xmlrpc.php`, WP-specific paths |
| DEBUG | `phpinfo.php`, Spring Actuator, test endpoints |
| CLOUD META | AWS/GCP/Azure metadata endpoints (SSRF testing) |
| CVE PATHS | Jenkins, Struts, Log4Shell-related known paths |

**Features:**
- Automatic severity rating — CRITICAL / HIGH / MEDIUM / LOW / INFO
- Concurrency control (3 to 20 simultaneous requests)
- Configurable delay between batches (stealth to max speed)
- Filter results by status code (200, 403, 401, 500)
- CSV export of all findings
- Live stats and progress bar

---

### 🌐 Module 2 — Subdomain Enumerator
Discovers subdomains using a combination of passive intelligence sources and active DNS brute forcing.

**Sources:**

| Source | Method | API Key Required? |
|--------|--------|------------------|
| crt.sh | SSL certificate transparency logs | No |
| HackerTarget | DNS host search API | No |
| AlienVault OTX | Passive DNS database | No |
| DNS Brute Force | Resolves words via Cloudflare DNS-over-HTTPS | No (uses your Worker) |

**Brute force wordlist sizes:**
- Small — ~50 most common subdomains
- Medium — ~150 words (recommended)
- Large — ~400 words including infrastructure, DevOps, and cloud terms

**Features:**
- Deduplication across all sources
- Live status probing on discovered subdomains
- Filter by source or live status
- CSV export of all results

---

### 🛡 Module 3 — HTTP Header Analyzer
Fetches all HTTP response headers from a target URL and performs a detailed security analysis across six categories.

**Checks performed:**

| Category | Headers Analyzed |
|----------|-----------------|
| Security Headers | Content-Security-Policy, Strict-Transport-Security, X-Frame-Options, X-Content-Type-Options, Referrer-Policy, Permissions-Policy, X-XSS-Protection |
| Information Leakage | Server, X-Powered-By, X-AspNet-Version, X-AspNetMvc-Version, X-Generator |
| CORS | Access-Control-Allow-Origin (wildcard detection), credentials + wildcard combo |
| Cookie Security | Secure flag, HttpOnly flag, SameSite attribute |
| TLS / HSTS | max-age value, includeSubDomains, preload |
| Caching | Cache-Control configuration |

**Output:**
- Security score out of 100
- Letter grade (A+ to F)
- Per-finding remediation recommendations
- Raw headers view
- Count of missing headers and issues found

---

## 🚀 Setup Guide

### Step 1 — Deploy the Cloudflare Worker

The tool requires a Cloudflare Worker to proxy requests. This is necessary because browsers block direct cross-origin API calls from static GitHub Pages sites (CORS restriction). The Worker handles all three modules — directory probing, DNS lookups, and header fetching.

**Why Cloudflare?** Their free tier allows 100,000 requests per day — more than enough for personal security research.

1. Go to [workers.cloudflare.com](https://workers.cloudflare.com) and sign up (no credit card needed)

2. In the Cloudflare dashboard click **Workers & Pages → Create Application → Create Worker**

3. Name it something like `welchsec-proxy` and click **Deploy**

4. Click **Edit Code**, select all the default code and delete it

5. Copy the contents of `cloudflare-worker.js` from this repo and paste it in

6. Click **Deploy**

7. Copy your Worker URL — it will look like:
   ```
   https://welchsec-proxy.yourname.workers.dev
   ```

> **Already have a Worker from the TTP Report or Intrusion Report?**
> Just update your existing worker with the new `cloudflare-worker.js` — it handles all tools in one. Your existing Worker URL stays the same.

---

### Step 2 — Use the Tool

1. Open [welchsec.github.io/Recon-Tool](https://welchsec.github.io/Recon-Tool/)
2. Paste your **Cloudflare Worker URL** into the Worker URL field
3. Select a module from the top navigation bar
4. Enter your target and configure options
5. Click the scan/analyze button

No API keys are required for any of the three modules.

---

## 💡 Usage Tips

### Directory Scanner
- Start with **Stealth mode** (3 concurrent, 500ms delay) on bug bounty targets — aggressive scanning can get you flagged or banned from programs
- Check the **Critical tab first** after a scan — that's where the high-value findings like exposed `.env` files, `.git/` directories, and backup archives appear
- **Export to CSV** after each scan to include findings in your bug bounty reports
- Always verify the target domain is **within the program's defined scope** before scanning

### Subdomain Enumerator
- Run **all passive sources first** (no brute force) for a quick low-noise discovery pass
- Use **Medium brute force** wordlist as a good balance between coverage and speed
- Subdomains marked **LIVE** have been confirmed as responding to HTTP requests
- Discovered subdomains can be fed directly into the Directory Scanner for further recon

### Header Analyzer
- A score below **70** generally indicates significant security header gaps worth reporting
- **Missing CSP** and **missing HSTS** are often reportable findings on bug bounty programs
- **Wildcard CORS + credentials** is typically a HIGH or CRITICAL finding
- **Info leakage headers** (Server, X-Powered-By) are usually LOW severity but worth noting
- Always check program policy — some programs exclude informational findings

---

## 💰 Cost

Everything is completely free:

| Component | Cost |
|-----------|------|
| GitHub Pages hosting | Free |
| Cloudflare Worker | Free (100k requests/day) |
| crt.sh API | Free, no key |
| HackerTarget API | Free tier, no key |
| AlienVault OTX API | Free, no key |
| Cloudflare DNS-over-HTTPS | Free |

---

## 🔧 Troubleshooting

**"Failed to fetch" or no results appearing**
Your Cloudflare Worker URL is missing or incorrect. Make sure you have deployed the worker and pasted the full URL including `https://`.

**Directory scan returns nothing**
Check the status filter dropdown — it may be set to "200 Only" while the server is returning 403s or redirects. Switch to "Interesting" or "All" to see more results.

**Subdomain enumeration returns 0 from crt.sh**
The domain may have no SSL certificates logged, or crt.sh may be temporarily rate-limiting. Try again after a few minutes.

**DNS brute force is very slow**
Reduce the wordlist size to Small or reduce concurrency. The Worker has a 30-second execution limit per request on the free tier.

**Header analyzer shows an error**
Some servers block automated requests. Try adding `www.` to the domain or switching between `http://` and `https://`.

**GitHub Pages shows the README instead of the tool**
The HTML file must be named exactly `index.html`. Rename it in GitHub if needed.

---

## 🔗 Related Tools

| Tool | Description | Link |
|------|-------------|------|
| TTP Report | AI-powered MITRE ATT&CK threat intelligence | [welchsec.github.io/TTP-Threat-Report](https://welchsec.github.io/TTP-Threat-Report/) |
| Intrusion Report | DFIR-style reports from live threat intel feeds | [welchsec.github.io/Intrusion-Report](https://welchsec.github.io/Intrusion-Report/) |

All tools share the same Cloudflare Worker — one deployment handles everything.

---

## 📬 Contact

Built by [@welchsec](https://github.com/welchsec)
