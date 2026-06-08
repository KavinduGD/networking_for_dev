## Layer 7 Firewall (Application Layer Firewall)

A **Layer 7 firewall** operates at the **Application Layer** of the OSI model, meaning it can inspect, filter, and control traffic based on the actual _content_ of data — not just IP addresses and ports.

### How it works

Traditional firewalls (Layer 3/4) only see:

- Source/destination IP
- Port numbers
- Protocol (TCP/UDP)

A Layer 7 firewall goes deeper — it **reassembles and reads the actual payload** of packets to understand _what application_ is communicating and _what it's doing_.

### Step-by-step process

**1. Packet Reassembly**
Raw packets arrive and the firewall reassembles them into complete application-layer streams (e.g., a full HTTP request).

**2. Protocol Identification**
It identifies the application protocol — HTTP, DNS, SMTP, TLS, etc. — even if it's running on a non-standard port (e.g., HTTP on port 8080).

**3. Deep Packet Inspection (DPI)**
The firewall reads the content:

- HTTP: inspects URL, headers, method (GET/POST), cookies, body
- DNS: checks the queried domain name
- TLS: inspects the SNI (Server Name Indication) even without decrypting
- SMTP: reads sender, recipient, subject

**4. Policy Matching**
Rules are applied based on application-level context:

- Block `Facebook.com` even over HTTPS
- Allow `GET` but block `POST` to a specific API
- Block `.exe` file downloads
- Rate-limit requests to a login endpoint

**5. Action**
The firewall **allows, blocks, logs, rate-limits, or redirects** the traffic based on matched rules.

---

### What it can detect that L3/L4 can't

| Capability                          | L3/L4 Firewall | L7 Firewall |
| ----------------------------------- | -------------- | ----------- |
| Block by IP/port                    | ✅             | ✅          |
| Block specific URLs                 | ❌             | ✅          |
| Identify apps (e.g., Zoom vs HTTPS) | ❌             | ✅          |
| Block file types in uploads         | ❌             | ✅          |
| Detect SQL injection in requests    | ❌             | ✅          |
| Inspect DNS queries                 | ❌             | ✅          |
| TLS/SNI inspection                  | ❌             | ✅          |

---

### TLS Traffic (the tricky part)

Most traffic today is encrypted. L7 firewalls handle this two ways:

- **SNI Inspection** — reads the hostname in the TLS handshake _before_ encryption begins (no decryption needed)
- **TLS Interception (man-in-the-middle)** — the firewall decrypts traffic using its own certificate, inspects it, then re-encrypts — used in enterprise WAFs and next-gen firewalls

---

### Common use cases

- **WAF (Web Application Firewall)** — blocks SQLi, XSS, CSRF at the HTTP level
- **NGFW (Next-Gen Firewall)** — app-aware corporate firewalls (Palo Alto, Fortinet)
- **API Gateway** — rate limiting, auth enforcement, schema validation
- **CDN Edge Rules** — Cloudflare, AWS WAF filtering requests at the edge

---

### Key trade-off

> L7 inspection is **powerful but expensive** — reading and parsing every packet's payload requires significantly more CPU than simple IP/port matching, which is why it's often offloaded to dedicated hardware or edge nodes.
