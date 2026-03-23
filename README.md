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

The suite contains **9 modules** accessible from the top navigation bar. Enter your Cloudflare Worker URL once and it syncs across all modules automatically.

---

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

### 🔌 Module 3 — Port Scanner
Probes a target host for open ports and identifies exposed services. Flags each finding by risk level.

**Port groups you can toggle:**

| Group | Ports Included |
|-------|---------------|
| COMMON | 21, 22, 23, 25, 53, 80, 110, 443, 8080, 8443 |
| WEB | 80, 443, 3000, 4443, 8000, 8080, 8443, 8888, 9000 |
| DATABASES | 1433 (MSSQL), 1521 (Oracle), 3306 (MySQL), 5432 (PostgreSQL), 6379 (Redis), 9200 (Elasticsearch), 27017 (MongoDB) |
| REMOTE ACCESS | 22 (SSH), 23 (Telnet), 3389 (RDP), 5900 (VNC) |
| MAIL | 25, 110, 143, 587, 993, 995 |
| DEVOPS | 2375/2376 (Docker), 4243, 8080, 9000, 50000 (Jenkins) |

**Risk levels:**
- CRITICAL — RDP, SMB, Telnet, unencrypted databases, Docker API
- HIGH — SSH, RPC, NetBIOS, Jupyter, PHP-FPM
- MEDIUM — Dev servers, mail ports, HTTP alternatives
- LOW — Standard web ports (80, 443)

You can also enter custom ports in the additional ports field.

---

### 🛡 Module 4 — HTTP Header Analyzer
Fetches all HTTP response headers from a target URL and performs a detailed security analysis across six categories.

**Checks performed:**

| Category | Headers Analyzed |
|----------|-----------------|
| Security Headers | Content-Security-Policy, Strict-Transport-Security, X-Frame-Options, X-Content-Type-Options, Referrer-Policy, Permissions-Policy, X-XSS-Protection |
| Information Leakage | Server, X-Powered-By, X-AspNet-Version, X-Generator |
| CORS | Access-Control-Allow-Origin (wildcard detection), credentials + wildcard combo |
| Cookie Security | Secure flag, HttpOnly flag, SameSite attribute |
| TLS / HSTS | max-age value, includeSubDomains, preload |
| Caching | Cache-Control configuration |

**Output:**
- Security score out of 100 with letter grade (A+ to F)
- Per-finding remediation recommendations
- Raw headers view
- Count of missing headers and issues found

---

### 📜 Module 5 — JavaScript File Analyzer
Crawls a target page, finds all JavaScript files, and scans them for hardcoded secrets, API keys, internal URLs, and API endpoints.

**Secret patterns detected:**

| Type | Severity |
|------|----------|
| AWS Access Key (`AKIA...`) | CRITICAL |
| GitHub Token (`ghp_...`, `ghs_...`) | CRITICAL |
| Stripe Live Key (`sk_live_...`) | CRITICAL |
| Slack Token (`xoxb-...`) | CRITICAL |
| Private Key (`-----BEGIN PRIVATE KEY-----`) | CRITICAL |
| Basic Auth in URL | CRITICAL |
| Google API Key (`AIza...`) | HIGH |
| Firebase URL | MEDIUM |
| S3 Bucket reference | MEDIUM |
| JWT Token | MEDIUM |
| Internal IP addresses | LOW |
| Internal URLs (localhost, intranet) | MEDIUM |

Also extracts API endpoints matching common patterns (`/api/`, `/v1/`, `/auth/`, `/admin/` etc.)

---

### 🌍 Module 6 — WHOIS & DNS Inspector
Performs full DNS record enumeration and RDAP/WHOIS lookups for a domain.

**DNS record types queried:** A, AAAA, MX, NS, TXT, CNAME, SOA, CAA

**Also checks:**
- SPF record (email spoofing protection)
- DMARC record (email authentication policy)
- MX configuration
- RDAP registration data (registrar, registration date, expiry, registrant)

---

### 🖥 Module 7 — Technology Fingerprinter
Identifies the technology stack running on a target by analyzing HTTP headers and HTML source.

**Detects 25+ technologies across categories:**

| Category | Examples |
|----------|---------|
| CMS | WordPress, Drupal, Joomla, Magento, Shopify |
| Language | PHP, ASP.NET |
| Framework | Laravel, Django, Ruby on Rails, Express.js, Next.js |
| Library | React, Vue.js, Angular |
| Server | nginx, Apache, IIS |
| CDN | Cloudflare, Fastly |
| Security | reCAPTCHA, Cloudflare WAF |
| Analytics | Google Analytics, Hotjar |

Also extracts version information from response headers (Server, X-Powered-By, X-AspNet-Version etc.)

---

### ☁ Module 8 — S3 Bucket Finder
Checks for publicly accessible or misconfigured AWS S3 buckets based on common naming patterns derived from the target company name.

**Checks variations like:**
- `companyname.s3.amazonaws.com`
- `companyname-backup`, `-dev`, `-staging`, `-prod`
- `companyname-assets`, `-static`, `-media`, `-uploads`
- `www.companyname`, `api.companyname`, `cdn.companyname`

**Result statuses:**
- **PUBLIC** — Bucket is publicly readable (CRITICAL finding)
- **EXISTS** — Bucket exists but access is denied (worth noting)
- **NOT FOUND** — Bucket does not exist

You can also add custom name variations to check in the additional names field.

---

### 📄 Module 9 — Report Generator
Compiles findings from all modules into a formatted bug bounty report.

**Features:**
- Aggregates findings from all 9 modules automatically
- Severity-sorted findings with module attribution
- Summary statistics (Critical / High / Medium / Low / Info counts)
- Toggle which module results to include
- Export to `.TXT` file ready to attach to a bug bounty submission

---

## 🚀 Setup Guide

### Step 1 — Deploy the Cloudflare Worker

The tool requires a Cloudflare Worker to proxy requests. This is necessary because browsers block direct cross-origin API calls from static GitHub Pages sites (CORS restriction). The Worker handles all nine modules.

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
2. Paste your **Cloudflare Worker URL** into the Worker URL field in any module — it syncs automatically to all other modules
3. Select a module from the top navigation bar
4. Enter your target and configure options
5. Click the scan button
6. Use the **Report** module at the end to compile all findings

No API keys are required for any module.

---

## 💡 Usage Tips

### Recommended Workflow for Bug Bounty
1. Start with **WHOIS/DNS** — understand the target's infrastructure first
2. Run **Subdomain Enumerator** — map the full attack surface
3. Run **Tech Fingerprinter** on key subdomains — know what you're dealing with
4. Run **Header Analyzer** on the main domain and key subdomains
5. Run **Dir Scanner** on interesting subdomains with relevant wordlists
6. Run **JS Analyzer** on the main app — often finds high-severity secrets
7. Run **Port Scanner** on non-web subdomains and IP addresses
8. Run **S3 Bucket Finder** using the company name
9. Compile everything in the **Report** module and export

### Directory Scanner
- Start with **Stealth mode** (3 concurrent, 500ms delay) on bug bounty targets
- Check the **Critical tab first** — exposed `.env` files, `.git/` directories, and backup archives
- **Export to CSV** after each scan for your bug bounty report

### Port Scanner
- CRITICAL ports open to the internet (RDP 3389, SMB 445, databases) are often high-severity findings
- Unencrypted database ports (3306, 5432, 27017) publicly exposed are almost always reportable
- Docker API on port 2375 without TLS is typically a critical finding

### JS Analyzer
- Always analyze the main application — production JS files frequently contain accidentally committed secrets
- AWS keys and GitHub tokens found in JS are almost always CRITICAL findings on any bug bounty program
- Run on each major subdomain separately for best coverage

### Header Analyzer
- Score below **70** generally indicates reportable findings
- **Wildcard CORS + credentials** is typically CRITICAL
- **Missing CSP** and **missing HSTS** are commonly accepted findings

### S3 Bucket Finder
- Try variations of the company name, product name, and domain without TLD
- A PUBLIC bucket is almost always a CRITICAL or HIGH finding
- Even an EXISTS bucket is worth noting — it confirms bucket naming conventions

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
| RDAP lookup | Free |

---

## 🔧 Troubleshooting

**"Failed to fetch" or no results appearing**
Your Cloudflare Worker URL is missing or incorrect. Make sure you have deployed the worker and pasted the full URL including `https://`.

**Directory scan returns nothing**
Check the status filter dropdown — it may be set to "200 Only". Switch to "Interesting" or "All" to see more results.

**Subdomain enumeration returns 0 from crt.sh**
The domain may have no SSL certificates logged, or crt.sh may be temporarily rate-limiting. Try again after a few minutes.

**Port scanner shows no open ports**
The Worker may be timing out on filtered ports. Try scanning fewer port groups at once, or check if the target is behind a firewall that drops packets rather than rejecting them.

**JS Analyzer finds no secrets**
The target may minify or bundle their JS in a way that obscures patterns, or secrets may be stored server-side. Try analyzing individual JS file URLs directly.

**S3 check returns no results**
Try different name variations — use the company name, product name, and domain name separately. Also try without hyphens and with common abbreviations.

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
