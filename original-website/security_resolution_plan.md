# Phoenix Evolution Website Rebuild — Security Resolution Plan

This document maps out the specific security vulnerabilities (cases) of our current WordPress platform and how the new Static HTML architecture permanently resolves them.

---

### Security Matrix: Threat Cases & Resolutions

| # | Security Threat (The Case) | Why Old Site is Vulnerable (WordPress) | How Rebuilt Site Solves It (Static HTML) | Business & Client Impact |
|---|---|---|---|---|
| **1** | **Client-Side Virus Infection** *(Malware injection)* | Hackers compromise the server or database and inject scripts that force users' browsers to download viruses. | **No Server Execution or DB:** Plain HTML files are read-only. There is no backend database or dynamic code execution to inject malware into. | **Guaranteed Client Safety:** Clients can never be infected with malware through our website. |
| **2** | **Vulnerable Third-Party Plugins** | Uses plugins (Revolution Slider, MetForm, etc.) that have frequent, unpatched security vulnerabilities. | **Zero Plugin Dependencies:** Replaced all sliders and forms with native client-side CSS and vanilla JavaScript. No plugins to hack. | **Zero Maintenance:** No need for weekly security patch updates or monitoring. |
| **3** | **Admin Portal Exploitation** *(Brute-force / Credential Stuffing)* | Publicly accessible login page (`/wp-admin`) allows attackers to attempt password guessing or exploit login session bypasses. | **No Admin Portal:** The site has no login panel or CMS database. Content changes are pushed securely via version-controlled code. | **Zero Credentials at Risk:** No administrative credentials exist on the public web server to be stolen. |
| **4** | **Server Takeover** *(Remote Code Execution / Shell Uploads)* | Attackers exploit file-upload forms or outdated PHP versions to upload control scripts and take over the web server. | **Static-only Serving:** The server only delivers pre-defined text and images. It does not run PHP or support code execution. | **Unhackable Web Server:** Host server cannot be used as a staging ground for other cyber attacks. |
| **5** | **DDoS & Traffic Spikes** *(Service Downtime)* | High traffic or a DDoS attack overwhelms the database server, causing database connection errors and crashing the website. | **CDN Edge Distribution:** Static files are served directly from global CDN edges (e.g. Cloudflare). No database queries are run. | **99.99% Uptime:** The site can handle massive traffic spikes with zero performance degradation or downtime. |

---

## Technical Security Implementations & Pending Decisions

We have identified five key implementations and decisions that need to be finalized before deployment:

### 1. Contact Form Backend Handler
* **The Case:** Static HTML sites do not have a server-side backend to process form inputs.
* **The Solution:** We will integrate a secure, serverless form processing API (e.g., **Netlify Forms**, **Cloudflare Pages Functions**, or **Formspree**). 
* **Security Benefit:** Form data is securely sent over HTTPS to the API handler. No SMTP credentials or server passwords are exposed in the frontend code.

### 2. Spam & Bot Protection (CAPTCHA)
* **The Case:** Open web contact forms are targeted by automated spam bots.
* **The Solution:** Implement **hCaptcha** or **Google reCAPTCHA v3** (silent verification).
* **Security Benefit:** Verifies that a human is submitting the form before transmitting data, preventing bot spam and keeping our contact inbox clean.

### 3. Content Security Policy (CSP)
* **The Case:** Malicious script injection (XSS) from unauthorized third-party tools or compromised local files.
* **The Solution:** Configure a Content Security Policy (CSP) header at the hosting level.
* **Security Benefit:** The browser will only load scripts from explicitly approved domains (e.g., Google Fonts, FontAwesome, hCaptcha), automatically blocking any unrecognized or injected external scripts.

### 4. External Scripts Audit & Supply Chain Security
* **The Case:** Future integration of external scripts (e.g., Google Analytics, live chat widgets) could introduce compromised code if a third-party domain is hacked.
* **The Solution:** Establish an external script loading policy.
* **Security Benefit:** All future scripts must be loaded asynchronously (`async`/`defer`) and utilize **Subresource Integrity (SRI)** hashes where available to guarantee the files have not been tampered with.

### 5. Hosting Migration (GoDaddy vs. Cloudflare/Netlify)
* **The Case:** We currently host the site on GoDaddy. We must choose between staying on GoDaddy or migrating to Cloudflare/Netlify.
* **Proposed Choices:**
  * **Option A: Migrate to Cloudflare Pages or Netlify (Recommended)**
    * *Pros:* Automated SSL, native form handling, integrated global CDN, automated deployments via Git, zero server maintenance.
    * *Cons:* Requires a DNS nameserver update to point our domain to the new provider.
  * **Option B: Stay on GoDaddy (Deploy Static Build)**
    * *Pros:* No DNS nameserver migration required; we just upload the static HTML/CSS/JS files directly to our existing GoDaddy web root via FTP or cPanel. (This still removes the WordPress vulnerability entirely since the PHP/WP stack is deleted).
    * *Cons:* Requires a third-party form backend subscription (like Formspree) and manual verification of SSL certificate status.
