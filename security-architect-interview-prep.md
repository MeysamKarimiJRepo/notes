# Security Architect Interview Prep — Energize Global Services (Porto)

> Prepared for: Meysam — Senior Java / Spring Boot engineer (13+ yrs, fintech/payments), CCNA, Windows Server 2008 network infrastructure, ISA Server 2002, M.Sc. Software Engineering.
> Role focus areas (from the job ad): **Network & Edge (WAF)**, **Vulnerability Management**, **Pentest findings closure**, **Threat Detection & Deception (honeypots)**, **SaaS / Cloud application assurance**, **Risk communication**.

---

## Table of Contents

0. [Your Positioning Strategy](#0-your-positioning-strategy)
1. [Security Fundamentals](#1-security-fundamentals)
2. [Network Security](#2-network-security)
3. [Edge Security & WAF](#3-edge-security--waf)
4. [Web Application & API Security](#4-web-application--api-security)
5. [Identity & Access Management](#5-identity--access-management)
6. [Vulnerability Management](#6-vulnerability-management)
7. [Penetration Testing & Findings Closure](#7-penetration-testing--findings-closure)
8. [Threat Detection, Monitoring & Deception](#8-threat-detection-monitoring--deception)
9. [Cloud & SaaS Security Assurance](#9-cloud--saas-security-assurance)
10. [Cryptography & PKI Essentials](#10-cryptography--pki-essentials)
11. [Frameworks, Standards & Regulation](#11-frameworks-standards--regulation)
12. [Secure SDLC, DevSecOps & Containers](#12-secure-sdlc-devsecops--containers)
13. [Risk Communication & Influencing](#13-risk-communication--influencing)
14. [Scenario / Architecture Questions](#14-scenario--architecture-questions)
15. [Behavioral Questions (STAR)](#15-behavioral-questions-star)
16. [Questions to Ask Them](#16-questions-to-ask-them)
17. [Final Memory Cheat Sheet](#17-final-memory-cheat-sheet)

---

## 0. Your Positioning Strategy

You are **not** coming from a classic SOC/pentester background — and that is fine. This role is an **architecture** role: define requirements, assess, prioritize, and drive remediation through engineering teams and vendors. Your story should be:

```
   ┌──────────────────────┐   ┌──────────────────────┐   ┌──────────────────────┐
   │  NETWORK FOUNDATION  │   │  BUILDER'S VIEW      │   │  FINTECH CONTEXT     │
   │  CCNA, Win2008 infra │ + │  13 yrs Java/Spring, │ + │  Core banking, payment│
   │  ISA Server (firewall│   │  microservices, K8s, │   │  platforms, regulated │
   │  /proxy = early edge)│   │  Kafka, CI/CD        │   │  (PCI, audits)        │
   └──────────┬───────────┘   └──────────┬───────────┘   └──────────┬───────────┘
              └──────────────────────────┼──────────────────────────┘
                                         ▼
                    "I understand how systems are BUILT, how traffic
                     FLOWS, and what the BUSINESS risk is — so I can
                     define security that engineers actually adopt."
```

**Key selling points**
- You speak the engineers' language → security requirements that are realistic, not theoretical.
- You understand the full request path: client → DNS → edge/WAF → load balancer → gateway → service → DB.
- You have worked in regulated financial environments (Worldline projects via EGS, core banking at OCS) → you know audit pressure, change control, availability constraints.
- You are an internal/known entity at EGS → you already know the delivery culture and stakeholders.

**Be honest about gaps, show a plan:** "My deepest hands-on work is application and platform security from the engineering side. For deception technology specifically, I have studied the approaches and would start with low-interaction honeypots and honeytokens, then mature." Interviewers trust candidates who know their boundaries.

### Sample "Tell me about yourself" (≈90 seconds)

> "I'm a senior software engineer and technical lead with over 13 years in financial systems — core banking at OCS supporting 1,500+ branches, high-throughput accounting services, and currently payment platforms for Worldline through EGS. Before and alongside development I built a network foundation — CCNA, Windows Server network infrastructure and ISA Server firewall/proxy training — so I've always looked at systems end-to-end, not just the code. In my recent roles I've owned architecture decisions, and security has been a constant part of that: authentication and authorization design in Spring microservices, securing Kafka and database access, handling vulnerability findings from scans and audits, and working with pentest results. I want to move formally into security architecture because the biggest gap I see in organizations isn't tools — it's translating risk into requirements engineering teams can deliver. That's exactly where my background fits."

---

## 1. Security Fundamentals

### Q1.1 What is the CIA triad? Give a fintech example for each.

```
                 CONFIDENTIALITY
                       /\
                      /  \        C → Card data (PAN) only seen by authorized
                     /    \           systems — encryption, access control
                    / DATA \      I → A transfer amount cannot be altered in
                   /________\         transit — signatures, hashing, MACs
           INTEGRITY          AVAILABILITY
                                  A → Payment gateway up 99.99% — redundancy,
                                      DDoS protection, capacity
```

Extensions worth mentioning: **Authenticity** and **Non-repudiation** (critical in payments — a customer cannot deny a signed transaction). In financial systems, integrity and availability are often weighted as heavily as confidentiality.

### Q1.2 What is AAA?

- **Authentication** — who are you? (password, MFA, certificate)
- **Authorization** — what are you allowed to do? (RBAC, ABAC, policies)
- **Accounting / Auditing** — what did you do? (logs, audit trails)

You know this from CCNA (RADIUS / TACACS+). TACACS+ separates the three A's and encrypts the whole payload; RADIUS combines authN/authZ and only encrypts the password.

### Q1.3 Explain Defense in Depth.

Multiple independent layers so that failure of one control doesn't mean compromise.

```
  ┌───────────────────────────────────────────────────┐
  │ POLICIES, TRAINING, GOVERNANCE                    │
  │  ┌─────────────────────────────────────────────┐  │
  │  │ PERIMETER / EDGE  (DDoS, CDN, WAF)          │  │
  │  │  ┌───────────────────────────────────────┐  │  │
  │  │  │ NETWORK  (segmentation, FW, IDS/IPS)  │  │  │
  │  │  │  ┌─────────────────────────────────┐  │  │  │
  │  │  │  │ HOST  (hardening, EDR, patching)│  │  │  │
  │  │  │  │  ┌───────────────────────────┐  │  │  │  │
  │  │  │  │  │ APPLICATION (authN/Z,     │  │  │  │  │
  │  │  │  │  │ input validation)         │  │  │  │  │
  │  │  │  │  │  ┌─────────────────────┐  │  │  │  │  │
  │  │  │  │  │  │ DATA (encryption,   │  │  │  │  │  │
  │  │  │  │  │  │ tokenization, DLP)  │  │  │  │  │  │
  │  │  │  │  │  └─────────────────────┘  │  │  │  │  │
  │  │  │  │  └───────────────────────────┘  │  │  │  │
  │  │  │  └─────────────────────────────────┘  │  │  │
  │  │  └───────────────────────────────────────┘  │  │
  │  └─────────────────────────────────────────────┘  │
  └───────────────────────────────────────────────────┘
       + MONITORING & DETECTION across every layer
```

Important nuance for an architect: layers must be **independent**. Two WAFs from the same vendor with the same ruleset are not two layers.

### Q1.4 What is Zero Trust?

"Never trust, always verify." Network location grants no trust. Every request is authenticated, authorized and encrypted, based on identity, device posture and context.

Core principles (NIST SP 800-207):
1. Verify explicitly (identity + device + context on every request)
2. Least privilege access (just-in-time, just-enough)
3. Assume breach (segment, encrypt, monitor, limit blast radius)

```
  OLD MODEL (castle & moat)          ZERO TRUST
  ┌──────────────────────┐          ┌──┐  ┌──┐  ┌──┐
  │  inside = trusted    │          │S1│  │S2│  │S3│   every service
  │   S1   S2   S3       │          └▲─┘  └▲─┘  └▲─┘   has its own
  └─────────▲────────────┘           │PEP  │PEP  │PEP  enforcement
            │ firewall               └──┬──┴──┬──┘     point
         outside                    Policy Decision Point
                                    (identity, device, risk)
```

PEP = Policy Enforcement Point, PDP = Policy Decision Point. In microservices: mTLS between services (service mesh), short-lived tokens, per-service authorization.

### Q1.5 Principle of Least Privilege vs Need-to-Know vs Separation of Duties?

- **Least privilege**: minimum permissions required to do the job, for the minimum time.
- **Need-to-know**: access to specific data only when required (even if clearance level allows more).
- **Separation of duties (SoD)**: no single person can complete a sensitive action alone — e.g., the developer who writes code cannot deploy to production alone; the person who creates a payee cannot also approve payment. Very common audit topic in banking.

### Q1.6 What is threat modeling? Which methods do you know?

Structured way to identify what can go wrong in a system **during design**, before code exists.

Four questions (Adam Shostack):
1. What are we working on? (data flow diagram)
2. What can go wrong? (STRIDE)
3. What are we going to do about it? (mitigations)
4. Did we do a good job? (validation)

**STRIDE** — memorize with the property each threat violates:

```
  ┌───┬────────────────────────┬──────────────────┬─────────────────────────┐
  │ S │ Spoofing               │ Authentication   │ fake login, stolen token│
  │ T │ Tampering              │ Integrity        │ modify amount in request│
  │ R │ Repudiation            │ Non-repudiation  │ "I never sent that"     │
  │ I │ Information disclosure │ Confidentiality  │ PAN in logs             │
  │ D │ Denial of service      │ Availability     │ flood payment API       │
  │ E │ Elevation of privilege │ Authorization    │ user → admin via IDOR   │
  └───┴────────────────────────┴──────────────────┴─────────────────────────┘
```

Other methods: **PASTA** (risk-centric, 7 stages, business-oriented), **LINDDUN** (privacy), **Attack Trees**, **DREAD** (older scoring, largely replaced by CVSS/risk matrices).

Draw trust boundaries on the DFD — threats cluster where data crosses a boundary (internet → DMZ, DMZ → internal, service → third-party SaaS).

### Q1.7 What does "Security by Design" mean to you practically?

- Security requirements are defined **at design time** (architecture review gate), not bolted on after pentest.
- Secure defaults (deny by default, TLS on, MFA on).
- Reusable secure building blocks — a hardened base image, a standard auth library, a pre-approved WAF policy template — so teams get security "for free".
- Automated checks in CI/CD (SAST, SCA, IaC scanning).
- Threat modeling for significant changes.

Good line: *"The cheapest vulnerability to fix is the one never designed in. My job is to make the secure path the easy path."*

### Q1.8 Risk, Threat, Vulnerability, Asset — define and relate.

```
   THREAT ─────exploits────▶ VULNERABILITY ────in────▶ ASSET
  (attacker,                (unpatched Log4j,         (payment API,
   malware)                  weak config)              customer data)
                                   │
                                   ▼
            RISK = Likelihood × Impact   (after existing controls)
```

- **Inherent risk**: before controls. **Residual risk**: after controls.
- Risk treatment options — **4 T's**: **Treat** (mitigate), **Transfer** (insurance, contract), **Tolerate** (accept, formally signed by a risk owner), **Terminate** (stop the activity / decommission).

---

## 2. Network Security

### Q2.1 Walk through the OSI model and give an attack/control at each layer.

```
  ┌────┬──────────────┬─────────────────────────┬──────────────────────────┐
  │ #  │ Layer        │ Example attacks         │ Controls                 │
  ├────┼──────────────┼─────────────────────────┼──────────────────────────┤
  │ 7  │ Application  │ SQLi, XSS, HTTP flood,  │ WAF, input validation,   │
  │    │              │ credential stuffing     │ rate limiting, bot mgmt  │
  │ 6  │ Presentation │ TLS downgrade, weak     │ TLS 1.2+/1.3, strong     │
  │    │              │ ciphers                 │ cipher suites, HSTS      │
  │ 5  │ Session      │ session hijacking       │ secure cookies, token    │
  │    │              │                         │ expiry, re-auth          │
  │ 4  │ Transport    │ SYN flood, port scans   │ SYN cookies, stateful FW │
  │ 3  │ Network      │ IP spoofing, ICMP flood,│ ACLs, uRPF, anti-spoof,  │
  │    │              │ route hijack (BGP)      │ RPKI, scrubbing centres  │
  │ 2  │ Data Link    │ ARP spoofing, MAC flood,│ DAI, port security,      │
  │    │              │ VLAN hopping            │ DHCP snooping, 802.1X    │
  │ 1  │ Physical     │ tapping, rogue devices  │ physical access control  │
  └────┴──────────────┴─────────────────────────┴──────────────────────────┘
   Mnemonic (top→down): "All People Seem To Need Data Processing"
```

This is where your CCNA shines — mention DHCP snooping, Dynamic ARP Inspection, port security, and native VLAN changes against VLAN hopping.

### Q2.2 Types of firewalls — what's the difference?

```
  Evolution ─────────────────────────────────────────────────────────▶

  PACKET FILTER      STATEFUL          PROXY / APP-LEVEL     NGFW
  (stateless ACL)    INSPECTION        GATEWAY               (Next-Gen)
  L3/L4 only         tracks sessions   terminates conn,      stateful + app ID
  src/dst/port       (conn table)      inspects L7           + IPS + user ID
  e.g. router ACL    e.g. Cisco ASA    e.g. ISA Server,      + TLS inspection
                                       Squid                 + threat intel
                                                             e.g. Palo Alto,
                                                             Fortinet
```

- **Stateless**: each packet judged alone; fast; needs rules both directions.
- **Stateful**: remembers established connections; return traffic allowed automatically.
- **Application proxy**: client talks to the proxy, proxy talks to server — full L7 visibility. **ISA Server 2002** was exactly this: a stateful firewall + forward/reverse web proxy + web publishing. Great bridge: *"ISA Server's web publishing rules were an early form of what a reverse proxy / WAF does today."* (ISA → TMG → discontinued; replaced in Microsoft world by Azure Application Gateway / Front Door WAF.)
- **NGFW**: application awareness regardless of port, user identity integration, IPS, URL filtering, TLS decryption.
- **WAF**: specialized L7 protection for HTTP(S) — see section 3.

### Q2.3 Design a network segmentation for a payment platform.

```
                           INTERNET
                              │
                  ┌───────────▼────────────┐
                  │ DDoS scrubbing / CDN   │
                  │ + WAF (edge)           │
                  └───────────┬────────────┘
                     ┌────────▼────────┐
                     │ External FW     │
                     └────────┬────────┘
     ┌────────────────────────▼─────────────────────────┐
     │ DMZ: reverse proxies, API gateway, LB            │
     └────────────────────────┬─────────────────────────┘
                     ┌────────▼────────┐
                     │ Internal FW     │ (different vendor = diversity)
                     └───┬─────────┬───┘
         ┌───────────────▼──┐   ┌──▼────────────────────┐
         │ APP ZONE          │   │ CDE (Card Data Env.)  │
         │ microservices     │   │ tokenization, HSM,    │
         │                   │──▶│ card DB — PCI scope   │
         └────────┬─────────┘   └───────────────────────┘
         ┌────────▼─────────┐   ┌───────────────────────┐
         │ DATA ZONE (DBs)  │   │ MGMT ZONE: bastion /  │
         └──────────────────┘   │ PAM, jump hosts, SIEM │
                                └───────────────────────┘
```

Key points to say:
- **Default deny** between zones; only documented flows allowed (flow matrix).
- **Cardholder Data Environment (CDE)** isolated to **reduce PCI DSS scope**.
- Admin access only via **bastion/PAM** with MFA and session recording — never direct SSH/RDP from user LAN.
- Microsegmentation inside zones (Kubernetes NetworkPolicies, security groups, service mesh).
- East-west traffic monitored, not just north-south.

### Q2.4 IDS vs IPS?

```
         IDS (passive)                      IPS (inline)
   traffic ──────────────▶ server     traffic ──▶[ IPS ]──▶ server
              │ copy (SPAN/TAP)                    │ can DROP
              ▼                                    ▼
          [ IDS ] → alert                        alert + block
```

- **IDS**: detects and alerts; no latency; can't stop an attack.
- **IPS**: inline, can block; risk of false-positive blocking legitimate traffic and becoming a single point of failure (fail-open vs fail-closed decision).
- Detection methods: **signature-based** (known patterns), **anomaly/behavior-based** (baseline deviations), **reputation-based**.
- NIDS/NIPS (network) vs HIDS/HIPS (host).

### Q2.5 Explain the TLS 1.2 vs 1.3 handshake and why 1.3 is better.

```
   TLS 1.3 (1-RTT)
   Client                                        Server
     │── ClientHello (+ key share, ciphers) ──────▶│
     │◀─ ServerHello (+ key share), Certificate,   │
     │   CertificateVerify, Finished (encrypted) ──│
     │── Finished ────────────────────────────────▶│
     │════════ Application data (encrypted) ═══════│
```

TLS 1.3 advantages:
- 1 round trip (vs 2 in 1.2); 0-RTT optional (but replay risk — avoid for non-idempotent requests like payments).
- Removed weak/legacy: RSA key exchange, CBC, RC4, SHA-1, static DH, compression, renegotiation.
- **Perfect Forward Secrecy mandatory** (ephemeral ECDHE) — compromise of the server private key doesn't decrypt past traffic.
- More of the handshake encrypted (certificate is encrypted).

Requirement you'd set: *"TLS 1.2 minimum with AEAD ciphers (AES-GCM, ChaCha20-Poly1305) and ECDHE; TLS 1.3 preferred; TLS 1.0/1.1 and SSL disabled; HSTS on public endpoints."*

### Q2.6 VPN types — IPsec vs SSL/TLS VPN?

- **IPsec** (L3): site-to-site tunnels between data centers/partners. Phases: IKE Phase 1 (secure channel, authenticate peers), Phase 2 (IPsec SAs for data). Modes: **tunnel** (whole packet encrypted, new header) vs **transport** (payload only). Protocols: **ESP** (encryption + integrity) vs **AH** (integrity only, breaks with NAT).
- **SSL/TLS VPN** (L4–7): remote users, clientless via browser or client-based.
- Modern direction: **ZTNA** (Zero Trust Network Access) replacing flat remote-access VPNs — access to specific apps, not the whole network.

### Q2.7 What are common DNS security concerns?

- **DNS spoofing / cache poisoning** → DNSSEC (signs records, provides integrity, not confidentiality).
- **DNS tunneling / exfiltration** → monitor query length/entropy, DNS firewall.
- **Subdomain takeover** → dangling CNAME pointing to a deprovisioned cloud resource; attacker claims it. Very common finding on public-facing estates → maintain DNS inventory, remove stale records.
- **Domain hijacking** → registrar lock, MFA on registrar account.
- **DoS on DNS** → anycast, managed DNS provider.
- Email-related DNS: **SPF, DKIM, DMARC** (anti-spoofing/phishing).

### Q2.8 What is NAT and is it a security control?

NAT translates private addresses to public. It hides internal addressing and incidentally blocks unsolicited inbound connections, but **it is not a security control by design** — it's an address-conservation mechanism. Don't rely on it instead of a firewall. (Nice nuance to show maturity.)

---

## 3. Edge Security & WAF

> This is a **core responsibility** of the role. Expect deep questions here.

### Q3.1 What is "the edge" and what components live there?

```
  User ──▶ DNS ──▶ ┌──────────────────── EDGE ─────────────────────┐ ──▶ Origin
                   │ Anycast network / CDN (caching, TLS term.)    │
                   │ DDoS protection (L3/L4 volumetric + L7)       │
                   │ WAF (OWASP rules, custom rules, virtual patch)│
                   │ Bot management (fingerprinting, challenges)   │
                   │ Rate limiting / API protection                │
                   │ TLS policy, certs, HSTS                       │
                   └───────────────────────────────────────────────┘
                     e.g. Cloudflare, Akamai, AWS CloudFront+WAF+Shield,
                          Azure Front Door, Imperva, F5
```

Principle: stop malicious traffic **as far away from the origin** as possible. The origin must then **accept traffic only from the edge** (IP allow-list, mTLS "authenticated origin pulls", or secret header) — otherwise attackers bypass the WAF by hitting the origin IP directly. This is a very common real-world gap — mention it!

### Q3.2 What is a WAF and how does it work?

A Web Application Firewall inspects HTTP(S) requests/responses at Layer 7 and blocks malicious patterns.

```
            ┌───────────────────── WAF ─────────────────────┐
  request ─▶│ 1. TLS termination (to see payload)           │
            │ 2. Normalization/decoding (URL, base64, etc.) │
            │ 3. Rule evaluation:                           │
            │    • Managed rules (OWASP CRS, vendor)        │
            │    • Custom rules (paths, geo, headers)       │
            │    • Rate limits / bot score                  │
            │ 4. Anomaly scoring → threshold                │
            │ 5. Action: ALLOW / BLOCK / CHALLENGE / LOG    │
            └───────────────────────┬───────────────────────┘
                                    ▼
                                 origin
```

**Security models:**
- **Negative model (blocklist)**: block known bad patterns (signatures, CRS). Easy to deploy, misses unknowns.
- **Positive model (allowlist)**: only allow known-good (schema validation — allowed methods, parameters, types, lengths). Stronger but needs maintenance; ideal for APIs with an OpenAPI spec.
- **Hybrid**: most mature programs.

**Deployment modes:** reverse proxy (most common), transparent bridge, cloud/edge-based, embedded/agent (RASP-like).

**OWASP Core Rule Set (CRS) + Paranoia Levels:** PL1 (few false positives, basic) → PL4 (very strict, many FPs). Anomaly scoring: each matched rule adds points; block when score ≥ threshold (e.g., 5 inbound).

### Q3.3 How would you roll out a WAF without breaking production?

```
  ┌─────────────┐   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
  │ 1. DISCOVER │──▶│ 2. MONITOR / │──▶│ 3. TUNE      │──▶│ 4. BLOCK     │
  │ inventory   │   │ DETECT ONLY  │   │ FP analysis, │   │ enforce,     │
  │ apps, APIs, │   │ (log mode)   │   │ exclusions   │   │ alert, keep  │
  │ owners      │   │ 2–4 weeks    │   │ per app/path │   │ tuning       │
  └─────────────┘   └──────────────┘   └──────────────┘   └──────┬───────┘
         ▲                                                        │
         └────────────── 5. CONTINUOUS REVIEW ◀───────────────────┘
```

- Start with high-confidence rules in block (e.g., known exploits, Log4Shell signatures) while broader rules log.
- Exclusions must be **narrow** (specific rule + specific parameter + specific path), documented, owned, and reviewed — never disable a whole rule group globally.
- Coordinate with app owners; have a rollback/bypass procedure with approval.
- Test in staging with regression and replayed traffic.

### Q3.4 What security requirements would you define for an edge/WAF deployment? (Very likely question!)

Structure the answer in categories — this shows architect thinking:

```
  ┌─────────────────────── EDGE SECURITY REQUIREMENTS ───────────────────────┐
  │ COVERAGE      All internet-facing HTTP(S) assets behind edge/WAF;        │
  │               asset inventory reconciled monthly; no unprotected origins │
  │ ORIGIN LOCK   Origin accepts traffic only from edge (IP allowlist/mTLS)  │
  │ TLS           TLS1.2+ (1.3 preferred), strong ciphers, HSTS, auto cert   │
  │               renewal, cert expiry monitoring                            │
  │ RULESETS      Managed OWASP/vendor rules in BLOCK mode; paranoia level   │
  │               defined per app criticality; virtual patching process      │
  │ API           Schema validation for APIs; authN at gateway; rate limits  │
  │ DDoS          L3/4 always-on; L7 rate limiting; runbook with provider    │
  │ BOTS          Bot management on login, registration, payment endpoints   │
  │ CHANGE MGMT   Rules as code (Terraform), peer review, staged rollout     │
  │ EXCEPTIONS    Narrow, documented, owner + expiry date, reviewed quarterly│
  │ LOGGING       Full logs to SIEM; retention per policy (e.g. 1 year PCI)  │
  │ MONITORING    Alerts: block spikes, rule disabled, mode changed to log,  │
  │               origin direct-hit attempts, cert expiry                    │
  │ ACCESS        Admin console via SSO + MFA, least privilege, audit log    │
  │ TESTING       Annual pentest incl. WAF bypass attempts; periodic         │
  │               validation (e.g., safe attack payload tests)               │
  │ RESILIENCE    Documented fail-open/fail-closed decision; HA; DR plan     │
  └──────────────────────────────────────────────────────────────────────────┘
```

Then explain **assessment**: review config vs these requirements, run test payloads (e.g., with an approved scanner) to confirm blocks, verify origin can't be hit directly, check log flow into SIEM, measure KPIs.

### Q3.5 How do you know your WAF is effective? What would you measure?

- **Coverage %**: internet-facing apps behind WAF / total internet-facing apps.
- **Enforcement %**: apps in block mode vs detect-only.
- **Exception count and age**: how many rule exclusions, how many past expiry.
- **False-positive rate**: tickets from business about blocked legitimate traffic.
- **Detection validation**: % of test attack payloads blocked (regular automated validation).
- **Time to virtual-patch** a new critical CVE.
- **Origin exposure**: count of origins reachable bypassing edge (target: 0).
- Blocked requests trends — useful context, but *not* an effectiveness measure by itself ("we blocked 1 million attacks" is a vanity metric).

### Q3.6 What is virtual patching?

Applying a WAF/IPS rule that blocks exploitation of a known vulnerability **before** the actual code fix is deployed. Buys time (e.g., Log4Shell: block `${jndi:` patterns and their obfuscations while teams upgrade). It is a **compensating control**, not a fix — track the real remediation with a deadline. Very relevant for **End-of-Life systems** that can't be patched.

### Q3.7 Can a WAF be bypassed? How?

Yes. Examples:
- Direct origin access (no origin lock).
- Encoding/obfuscation tricks (double URL encoding, Unicode, case variation, comments in SQL).
- HTTP request smuggling (front-end and back-end parse Content-Length/Transfer-Encoding differently).
- Oversized bodies — many WAFs only inspect the first N KB.
- Unusual content types (JSON, XML, multipart) not parsed by the WAF.
- **Business logic flaws, broken access control (IDOR)** — look like normal requests; WAF can't see them.

Conclusion: WAF is one layer; secure code and authorization are still required.

### Q3.8 How do DDoS attacks work and how do you defend?

```
  VOLUMETRIC (L3/4)        PROTOCOL (L3/4)          APPLICATION (L7)
  UDP floods, DNS/NTP/     SYN flood, ACK flood,    HTTP GET/POST flood,
  memcached amplification  Ping of death,           slowloris, expensive
  (Gbps / Tbps)            fragmented packets       search/login endpoints
        │                        │                        │
        ▼                        ▼                        ▼
  Anycast + scrubbing      SYN cookies, stateful    Rate limiting, bot mgmt,
  centres, upstream        edge, conn. limits       caching, challenges,
  provider                                          autoscaling, WAF rules
```

Also: a **DDoS runbook** with the provider (who to call, escalation, pre-agreed thresholds), and regular DDoS testing with approval.

### Q3.9 Rate limiting — how would you design it for a login and payment API?

- Limits per **IP**, per **account/user**, per **API key/client**, and per **device fingerprint** — IP-only fails against botnets and punishes shared NAT.
- Login: e.g., progressive delays, CAPTCHA/challenge after N failures, account lockout with care (lockout can itself become DoS).
- Payment initiation: stricter per-customer limits, anomaly detection, idempotency keys.
- Return `429 Too Many Requests` with `Retry-After`.
- Algorithms: token bucket / sliding window.

### Q3.10 Reverse proxy vs forward proxy vs load balancer vs API gateway?

```
  FORWARD PROXY:  [clients] ──▶ [proxy] ──▶ internet     (protects/controls clients;
                                                          ISA Server outbound web access)
  REVERSE PROXY:  internet ──▶ [proxy] ──▶ [servers]     (protects servers; ISA web publishing)
  LOAD BALANCER:  distributes traffic across servers (L4 or L7)
  API GATEWAY:    reverse proxy + API concerns: authN (JWT/OAuth validation),
                  rate limiting, routing, versioning, request transformation
```

---

## 4. Web Application & API Security

### Q4.1 List the OWASP Top 10 (2021) and give a one-line mitigation for each.

```
  ┌─────┬────────────────────────────────┬──────────────────────────────────────┐
  │ A01 │ Broken Access Control          │ Server-side authZ on every request,  │
  │     │ (IDOR, privilege escalation)   │ deny by default, object ownership chk│
  │ A02 │ Cryptographic Failures         │ TLS everywhere, strong algorithms,   │
  │     │                                │ encrypt sensitive data at rest       │
  │ A03 │ Injection (SQLi, XSS, cmd)     │ Parameterized queries, output        │
  │     │                                │ encoding, input validation           │
  │ A04 │ Insecure Design                │ Threat modeling, secure patterns     │
  │ A05 │ Security Misconfiguration      │ Hardened baselines, IaC, remove      │
  │     │                                │ defaults, disable debug/actuator     │
  │ A06 │ Vulnerable & Outdated Comps.   │ SCA, SBOM, patch process             │
  │ A07 │ Identification & AuthN Failures│ MFA, rate limit, secure session mgmt │
  │ A08 │ Software & Data Integrity Fail.│ Signed artifacts, secure CI/CD,      │
  │     │ (insecure deserialization)     │ avoid unsafe deserialization         │
  │ A09 │ Security Logging & Monitoring  │ Log security events, alert, retain   │
  │     │ Failures                       │                                      │
  │ A10 │ Server-Side Request Forgery    │ Allowlist outbound destinations,     │
  │     │                                │ block metadata IP 169.254.169.254    │
  └─────┴────────────────────────────────┴──────────────────────────────────────┘
  Mnemonic: "Bad Crypto Injects Insecure Misconfigured Vulnerable Auth,
             Integrity Logging SSRF"
```

**Also know the OWASP Top 10:2025 update** — main changes: Security Misconfiguration moved up to #2; a new **Software Supply Chain Failures** category (expands "Vulnerable & Outdated Components"); SSRF folded into Broken Access Control; a new **Mishandling of Exceptional Conditions** category (error handling, failing open). Mentioning you're aware of the update signals you keep current — verify the final list before the interview.

**OWASP API Security Top 10 (2023)** — top items to know: **API1 BOLA** (Broken Object Level Authorization = IDOR for APIs), API2 Broken Authentication, API3 Broken Object Property Level Authorization (mass assignment / excessive data exposure), API4 Unrestricted Resource Consumption, API5 Broken Function Level Authorization, API7 SSRF, API9 Improper Inventory Management (shadow/zombie APIs).

### Q4.2 Explain SQL injection and how to prevent it in Java.

Attacker input changes query structure:
```
  query = "SELECT * FROM users WHERE name='" + input + "'"
  input  = ' OR '1'='1
  result = SELECT * FROM users WHERE name='' OR '1'='1'   → returns all rows
```
Prevention:
- **PreparedStatement / parameterized queries**; JPA named parameters; never concatenate into JPQL/native queries.
- Least-privilege DB accounts (app user can't DROP tables).
- Input validation (allowlist) as secondary defense.
- WAF as an extra layer, not the fix.

### Q4.3 XSS types and prevention?

```
  STORED     payload saved in DB (comment field) → served to every viewer
  REFLECTED  payload in URL/param → reflected in response → victim clicks link
  DOM-BASED  client-side JS writes untrusted data into DOM (innerHTML)
```
Prevention: context-aware **output encoding** (HTML, attribute, JS, URL), frameworks that auto-escape (Thymeleaf, React), **Content Security Policy (CSP)** to block inline scripts, **HttpOnly** cookies so scripts can't read session tokens, sanitize rich HTML with a library (OWASP Java HTML Sanitizer).

### Q4.4 CSRF — what is it and how to prevent?

Victim's browser automatically sends cookies; attacker's site triggers a state-changing request (e.g., transfer money) to the bank.

Prevention: **anti-CSRF tokens** (synchronizer token — Spring Security enables by default), **SameSite=Lax/Strict** cookies, check Origin/Referer, require re-authentication for sensitive actions. Pure token-based APIs (Authorization header, not cookies) are not CSRF-vulnerable in the same way.

### Q4.5 SSRF — why is it dangerous in the cloud?

Server fetches a URL supplied by the user → attacker points it at internal resources:
```
  attacker ──"url=http://169.254.169.254/latest/meta-data/iam/..."──▶ app
  app ──▶ cloud metadata service ──▶ returns temporary cloud credentials!
```
(Capital One 2019 breach pattern.) Prevention: allowlist destinations, block private/link-local ranges after DNS resolution (beware DNS rebinding), enforce **IMDSv2** on AWS (session token required), egress filtering, separate fetcher service with no privileges.

### Q4.6 Which HTTP security headers would you require?

```
  Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
  Content-Security-Policy:   default-src 'self'; frame-ancestors 'none'; ...
  X-Content-Type-Options:    nosniff
  Referrer-Policy:           strict-origin-when-cross-origin
  Permissions-Policy:        camera=(), geolocation=()
  Cache-Control:             no-store   (for sensitive pages/responses)
  (X-Frame-Options: DENY — legacy; CSP frame-ancestors supersedes it)
  Cookies: Secure; HttpOnly; SameSite=Lax/Strict
  Remove: Server, X-Powered-By version banners
```

### Q4.7 JWT — common security pitfalls?

- `alg: none` accepted, or **algorithm confusion** (RS256 → HS256 using the public key as HMAC secret) → pin expected algorithm.
- Not validating `exp`, `iss`, `aud`.
- Long-lived tokens with no revocation → short access tokens (5–15 min) + refresh tokens with rotation.
- Sensitive data in payload (it's only base64, not encrypted).
- Tokens in localStorage (XSS-exposed) vs HttpOnly cookies (CSRF-considerations) — trade-off.
- Weak HMAC secrets → brute-forceable.

### Q4.8 How would you secure a Spring Boot microservice? (Your home ground — go deep.)

- Spring Security as OAuth2 **Resource Server** validating JWTs (issuer, audience, signature via JWKS).
- Method-level authorization (`@PreAuthorize`) + object ownership checks (prevent BOLA).
- **Actuator** endpoints: expose only `health`/`info` publicly; others restricted or on a separate management port. (Exposed `/actuator/env` or `/heapdump` leaks secrets — common pentest finding.)
- Secrets from Vault / cloud secret manager, never in `application.yml` in Git.
- Input validation with Bean Validation (`@Valid`), global exception handler that doesn't leak stack traces.
- Dependency scanning (OWASP Dependency-Check, Snyk, Dependabot); keep Spring Boot supported version.
- Mass assignment: use DTOs, not entities, for request binding.
- mTLS between services (service mesh like Istio/Linkerd) or signed service tokens.
- Kafka: TLS + SASL (SCRAM/OAUTHBEARER) + ACLs per topic; no PAN in plain messages.
- Structured security logging (login success/failure, authZ denials, admin actions) without sensitive data.

---

## 5. Identity & Access Management

### Q5.1 OAuth 2.0 vs OpenID Connect vs SAML?

```
  ┌──────────┬───────────────────────┬──────────────────┬────────────────────┐
  │          │ OAuth 2.0             │ OpenID Connect   │ SAML 2.0           │
  ├──────────┼───────────────────────┼──────────────────┼────────────────────┤
  │ Purpose  │ AUTHORIZATION         │ AUTHENTICATION   │ AUTHN (+ attrs)    │
  │          │ (delegated access)    │ on top of OAuth  │ enterprise SSO     │
  │ Token    │ Access token          │ ID token (JWT)   │ XML assertion      │
  │ Format   │ opaque or JWT         │ + access token   │ signed XML         │
  │ Typical  │ APIs, 3rd-party access│ modern web/mobile│ legacy enterprise  │
  │ use      │                       │ SSO              │ SaaS SSO           │
  └──────────┴───────────────────────┴──────────────────┴────────────────────┘
  Memory hook: OAuth = "valet key" (what you can do)
               OIDC  = "ID card"   (who you are)
               SAML  = "XML passport for enterprise"
```

### Q5.2 Draw the Authorization Code flow with PKCE.

```
  User/Browser        Client App            Authorization Server       API
      │  1. login click   │                         │                    │
      │──────────────────▶│ create code_verifier,   │                    │
      │                   │ code_challenge=SHA256(v)│                    │
      │◀── 2. redirect to /authorize?code_challenge=...                  │
      │─────────────────────────────────────────────▶│                   │
      │   3. user authenticates (+MFA), consents    │                    │
      │◀────────── 4. redirect back with ?code=XYZ ──│                   │
      │──────────────────▶│                         │                    │
      │                   │ 5. POST /token code +   │                    │
      │                   │    code_verifier ──────▶│ verifies hash      │
      │                   │◀── 6. access + ID + refresh tokens           │
      │                   │ 7. call API with Bearer token ──────────────▶│
      │                   │                         │   validates token  │
```

- **PKCE** protects against authorization code interception; now recommended for all clients (OAuth 2.1).
- **Implicit flow** and **Resource Owner Password flow** are deprecated.
- **Client Credentials** flow: service-to-service (no user).
- In open banking / PSD2 contexts: **FAPI** profile (mTLS or private_key_jwt client auth, PAR, sender-constrained tokens).

### Q5.3 What IAM requirements would you set for a SaaS application?

```
  ┌──────────────────────── SaaS IAM BASELINE ─────────────────────────┐
  │ SSO     SAML/OIDC with corporate IdP (Entra ID / Okta) — mandatory  │
  │ MFA     Enforced via IdP; phishing-resistant (FIDO2) for admins     │
  │ LOCAL   Local accounts disabled, except 1–2 break-glass accounts   │
  │         (vaulted, monitored, tested)                               │
  │ SCIM    Automated provisioning/deprovisioning (joiner-mover-leaver)│
  │ RBAC    Role mapping from IdP groups; least privilege; no shared   │
  │         accounts                                                   │
  │ ADMIN   Named admin accounts, separate from daily accounts          │
  │ REVIEW  Quarterly access reviews (certification) by owners         │
  │ API     API tokens/service accounts inventoried, scoped, rotated   │
  │ AUDIT   Admin & login logs exported to SIEM                        │
  │ SESSION Timeouts, conditional access (device, location, risk)      │
  └────────────────────────────────────────────────────────────────────┘
```

### Q5.4 What is PAM and why does it matter?

Privileged Access Management: vaulting privileged credentials, **just-in-time** elevation, session brokering/recording, credential rotation, approval workflows (e.g., CyberArk, BeyondTrust, Delinea, HashiCorp Boundary). Privileged accounts are attackers' primary target for lateral movement — especially Active Directory domain admins. (Your Windows Server background: tiered admin model — Tier 0 = domain controllers/identity, Tier 1 = servers, Tier 2 = workstations; never log into lower tiers with Tier 0 credentials.)

### Q5.5 RBAC vs ABAC vs ReBAC?

- **RBAC**: permissions via roles ("Teller", "Approver"). Simple; suffers role explosion.
- **ABAC**: policies on attributes (user dept, resource classification, time, location) — "managers can approve payments < €10k for their own branch during business hours".
- **ReBAC**: relationship-based (Google Zanzibar) — "user can view document if member of folder's team".

### Q5.6 What types of MFA are phishing-resistant?

```
  WEAKEST ───────────────────────────────────────────────────▶ STRONGEST
  SMS OTP    Email OTP    TOTP app    Push (w/ number   FIDO2/WebAuthn
  (SIM swap) (mailbox     (phishable  matching)         passkeys, smart
             compromise)  via AitM)                     cards (PIV)
                                                        ← phishing-resistant
```
Push without number matching is vulnerable to **MFA fatigue** (prompt bombing). Adversary-in-the-middle kits (Evilginx) steal session cookies after OTP — only origin-bound methods (FIDO2) resist.

---

## 6. Vulnerability Management

> **You will "own the strategy and architecture" of this program.** Expect the most questions here. Answer as a program owner, not as someone who runs a scanner.

### Q6.1 Describe the vulnerability management lifecycle.

```
                    ┌──────────────────────────┐
                    │ 0. ASSET INVENTORY       │  "You can't protect what
                    │ (CMDB, cloud, ext. ASM)  │   you don't know"
                    └────────────┬─────────────┘
                                 ▼
    ┌──────────────┐   ┌──────────────────┐   ┌─────────────────────┐
    │ 6. REPORT &  │   │ 1. DISCOVER /    │   │ 2. ASSESS &         │
    │ IMPROVE      │   │ SCAN             │──▶│ PRIORITIZE          │
    │ metrics,     │   │ infra, web, cloud│   │ CVSS + EPSS + KEV + │
    │ trends, root │   │ containers, code │   │ asset criticality + │
    │ causes       │   │ pentest, bounty  │   │ exposure            │
    └──────▲───────┘   └──────────────────┘   └──────────┬──────────┘
           │                                             ▼
    ┌──────┴───────┐   ┌──────────────────┐   ┌─────────────────────┐
    │ 5. VERIFY    │◀──│ 4. REMEDIATE /   │◀──│ 3. ASSIGN & TRACK   │
    │ rescan,      │   │ MITIGATE / ACCEPT│   │ owner, ticket, SLA  │
    │ retest       │   │                  │   │ (Jira)              │
    └──────────────┘   └──────────────────┘   └─────────────────────┘
```

Memory hook — six verbs in order: **Know → Find → Rank → Assign → Fix → Check → Learn** (inventory, scan, prioritize, ticket, remediate, verify, report).

### Q6.2 What is CVSS? Explain v3.1 and what changed in v4.0.

**CVSS** (Common Vulnerability Scoring System) scores **technical severity**, 0.0–10.0.

```
  0.0 None │ 0.1–3.9 Low │ 4.0–6.9 Medium │ 7.0–8.9 High │ 9.0–10.0 Critical
```

**CVSS v3.1 metric groups:**
```
  BASE (intrinsic, doesn't change)
   Exploitability: AV (Attack Vector: N/A/L/P), AC (Attack Complexity),
                   PR (Privileges Required), UI (User Interaction)
   Scope (S): Unchanged / Changed
   Impact: C / I / A  (High / Low / None)
  TEMPORAL (changes over time): Exploit maturity, Remediation level,
                                Report confidence
  ENVIRONMENTAL (your org): modified base metrics + security requirements
                            (CR/IR/AR) for the specific asset

  Example vector: CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H = 10.0 (Log4Shell)
```

**CVSS v4.0 (Nov 2023) changes:** Scope removed and replaced by **Vulnerable System vs Subsequent System** impacts; new **Attack Requirements (AT)**; Temporal renamed **Threat** (Exploit Maturity); new **Supplemental** metrics (Safety, Automatable, Recovery, etc.); naming **CVSS-B, CVSS-BT, CVSS-BE, CVSS-BTE** to show which groups were used.

**Key architect point:** *"CVSS measures severity, not risk. A CVSS 9.8 on an isolated internal test box can be less urgent than a CVSS 7.5 on an internet-facing payment API that is actively exploited."*

### Q6.3 What is EPSS, CISA KEV, and SSVC?

```
  ┌──────────┬──────────────────────────────────┬─────────────────────────────┐
  │ Signal   │ Answers the question             │ Source                      │
  ├──────────┼──────────────────────────────────┼─────────────────────────────┤
  │ CVSS     │ How BAD could it be?             │ FIRST / NVD / vendor        │
  │ EPSS     │ How LIKELY is exploitation in    │ FIRST — probability 0–1,    │
  │          │ the next 30 days?                │ updated daily               │
  │ CISA KEV │ Is it being exploited RIGHT NOW  │ CISA Known Exploited        │
  │          │ in the wild? (yes/no list)       │ Vulnerabilities catalog     │
  │ SSVC     │ What DECISION should I take?     │ CISA/CERT decision tree:    │
  │          │                                  │ Track / Track* / Attend /Act│
  └──────────┴──────────────────────────────────┴─────────────────────────────┘
```

Research shows only a small percentage (roughly 2–5%) of published CVEs are ever exploited in the wild — prioritizing by CVSS alone creates huge backlogs of "criticals" that don't matter while missing medium-scored exploited ones.

### Q6.4 How would you build a risk-based prioritization model? (Key question — practice aloud.)

Combine **threat** + **exposure** + **asset value**:

```
  PRIORITY = f( Severity  ×  Exploitability  ×  Exposure  ×  Asset criticality )
               (CVSS)      (KEV, EPSS,        (internet-  (business impact:
                            exploit public)     facing?     payments, PII,
                                                auth req?)  PCI scope)
                   minus  Compensating controls (WAF virtual patch, segmentation)
```

A simple, explainable tiering (what you'd propose):

```
  ┌──────┬─────────────────────────────────────────────────────┬────────────┐
  │ Tier │ Criteria                                            │ SLA (e.g.) │
  ├──────┼─────────────────────────────────────────────────────┼────────────┤
  │ P0   │ In CISA KEV / active exploitation AND internet-     │ 24–72 h    │
  │ EMERG│ facing or critical asset                            │ (+ virtual │
  │      │                                                     │ patch now) │
  │ P1   │ Critical/High CVSS + (EPSS high or public exploit)  │ 7–14 days  │
  │      │ on internet-facing or crown-jewel systems           │            │
  │ P2   │ High on internal systems; Critical w/ strong        │ 30 days    │
  │      │ compensating controls                               │            │
  │ P3   │ Medium                                              │ 90 days    │
  │ P4   │ Low / informational                                 │ best effort│
  │      │                                                     │ / next cyc.│
  └──────┴─────────────────────────────────────────────────────┴────────────┘
  (PCI DSS: critical/high patches within 1 month — Req. 6.3.3)
```

Principles to say:
- **Transparent and simple** enough that engineering teams understand why something is P1.
- Asset criticality comes from a maintained inventory tagged with owner, environment, data classification, exposure.
- **Automate** enrichment (scanner + EPSS/KEV feeds + CMDB tags) → auto-create tickets with the tier.
- Focus on **fixing root causes** (e.g., outdated base image causing 400 findings → one fix) — group by remediation action, not by CVE.

### Q6.5 How do you handle End-of-Life (EOL) systems? (Explicitly in the job ad!)

```
   EOL SYSTEM FOUND
        │
        ▼
   ┌─────────────────────┐   yes   ┌──────────────────────────────────┐
   │ Can it be upgraded/ │───────▶ │ Plan upgrade/migration with owner │
   │ replaced soon?      │         │ + deadline; track as project      │
   └─────────┬───────────┘         └──────────────────────────────────┘
             │ no / long time
             ▼
   ┌──────────────────────────────────────────────────────────────────┐
   │ COMPENSATING CONTROLS ("contain the risk"):                      │
   │  • Isolate: dedicated segment/VLAN, strict FW allow-list          │
   │  • Remove internet exposure; access only via jump host/proxy     │
   │  • Virtual patching (IPS/WAF)                                    │
   │  • Disable unused services/ports, harden                         │
   │  • Enhanced monitoring (EDR if supported, network detection,     │
   │    honeytokens nearby)                                           │
   │  • Extended support contract if available (e.g., ESU)            │
   │  • Backups tested; restore plan                                  │
   └──────────────────────────────┬───────────────────────────────────┘
                                  ▼
   ┌──────────────────────────────────────────────────────────────────┐
   │ FORMAL RISK ACCEPTANCE: documented residual risk, business owner │
   │ signs, expiry date (e.g. 6 months), reviewed at risk committee;  │
   │ decommission roadmap funded                                      │
   └──────────────────────────────────────────────────────────────────┘
```

Also: **prevent new EOL debt** — lifecycle tracking in CMDB (end-of-support dates), alerts 12 months ahead, EOL as a planning input for budgets, and a policy "no new deployments on software within 12 months of EOL". Tools like endoflife.date data can be automated.

A good sound bite: *"EOL is not a vulnerability, it's a guarantee of future vulnerabilities with no fix. So it's a lifecycle and funding problem as much as a technical one."*

### Q6.6 Public-facing exposures — how do you get visibility?

- **External Attack Surface Management (EASM)**: continuously discover internet-facing assets from the attacker's view — DNS enumeration, certificate transparency logs, cloud IP ranges, Shodan/Censys-style scanning (tools: Microsoft Defender EASM, Censys ASM, Tenable ASM, CyCognito).
- Compare discovered assets with the inventory → find **shadow IT**, forgotten test environments, exposed admin panels, dangling DNS.
- Prioritize public-facing: remote admin (RDP/SSH exposed), VPN appliances (frequent KEV entries: Ivanti, Fortinet, Citrix, Palo Alto), file transfer (MOVEit), unauthenticated web apps.

### Q6.7 Authenticated vs unauthenticated scanning? Agent vs network?

- **Unauthenticated**: attacker's external view; misses most missing patches.
- **Authenticated (credentialed)**: logs in, reads installed packages → far more accurate, fewer false positives. Credential must be vaulted and least-privilege.
- **Agent-based**: good for laptops/cloud/ephemeral hosts; continuous.
- **Container/image scanning**: in registry and CI (Trivy, Grype, Prisma, Wiz) — ephemeral containers should be fixed by **rebuilding the image**, not patching running containers.
- **Cloud (agentless snapshot scanning)**: Wiz, Orca, Defender for Cloud.
- Tools: Tenable Nessus/Tenable.io, Qualys VMDR, Rapid7 InsightVM, OpenVAS.

### Q6.8 What metrics/KPIs would you report? (Visibility of posture "over time" is in the job ad.)

```
  OPERATIONAL (for teams)             STRATEGIC (for leadership)
  ─────────────────────────            ──────────────────────────
  • Open vulns by tier & owner          • % within SLA (target e.g. 95% P0/P1)
  • MTTR by tier                        • Risk trend: exposure of internet-
  • SLA breaches / aging buckets          facing critical assets over time
    (0-30, 31-60, 61-90, 90+ days)      • KEV vulns open (target: 0)
  • Scan coverage % (assets scanned     • EOL systems count & trend
    / assets in inventory)              • Pentest findings open by severity
  • Authenticated scan success %          and age
  • Reopen rate (fixes that didn't      • Accepted risks: count, expiring
    stick)                              • Top 5 risk drivers & owners
```

Present as **trend lines** ("burn-down"), not snapshots. Avoid vanity metrics like "total vulns found". Tie metrics to decisions ("Team X is 40% over SLA because of EOL Windows 2012 — we need budget for migration").

### Q6.9 How do you handle risk exceptions / acceptance?

- Formal process: justification, compensating controls, residual risk rating, **business owner** (not IT) signs, security gives an opinion, **expiry date**, periodic review.
- Exceptions tracked in a register and reported — never "silent" ignores in the scanner.
- Authority to accept scales with risk level (e.g., Critical requires CISO/executive).

### Q6.10 A critical zero-day (like Log4Shell) is announced. Walk me through your response.

```
  T+0h   Triage: read advisory, confirm affected versions, exploit status,
         CVSS/KEV. Open incident / war room with clear owner.
  T+2h   Scope: query SBOM/SCA, CMDB, scanners, code search, container
         registries → which assets contain it? Prioritize internet-facing.
  T+4h   Contain: WAF/IPS virtual patch (block exploit patterns), config
         mitigations (e.g. log4j2.formatMsgNoLookups — noted later as
         insufficient), egress restrictions (block outbound LDAP/RMI).
  T+8h   Hunt: search logs/SIEM for exploitation attempts & IOCs since
         disclosure (or earlier); check for successful callbacks.
  T+24h+ Remediate: patch/upgrade in priority order; vendor/SaaS
         outreach (third-party exposure questionnaire).
  Then   Verify (rescan), communicate status regularly to leadership,
         post-incident review: how fast could we find it? → improve SBOM.
```

Point to emphasize: **SBOM + asset inventory** determine how fast you answer "are we affected?".

---

## 7. Penetration Testing & Findings Closure

### Q7.1 Vulnerability scan vs pentest vs red team vs bug bounty?

```
  ┌──────────────┬──────────────────┬────────────────┬────────────────────┐
  │              │ Goal             │ Depth          │ Frequency          │
  ├──────────────┼──────────────────┼────────────────┼────────────────────┤
  │ Vuln scan    │ Find known vulns │ Broad, shallow,│ Continuous/weekly  │
  │              │ automatically    │ automated      │                    │
  │ Pentest      │ Exploit & prove  │ Deep, manual,  │ Annual + major     │
  │              │ impact in scope  │ time-boxed     │ changes (PCI 11.4) │
  │ Red team     │ Test detection & │ Objective-based│ Periodic, mature   │
  │              │ response (people,│ stealthy, full │ orgs               │
  │              │ process, tech)   │ kill chain     │                    │
  │ Purple team  │ Red + Blue work  │ Collaborative, │ Workshops          │
  │              │ together to tune │ detection-focus│                    │
  │ Bug bounty   │ Crowd finds vulns│ Continuous,    │ Ongoing            │
  │              │ in public assets │ pay per bug    │                    │
  └──────────────┴──────────────────┴────────────────┴────────────────────┘
```

### Q7.2 Black / grey / white box?

- **Black box**: no information — realistic external attacker, but time wasted on recon.
- **Grey box**: some info (user credentials, architecture diagrams) — best value for most apps.
- **White box**: full access to code/design — deepest coverage.

### Q7.3 Phases of a penetration test?

```
  1. PRE-ENGAGEMENT ─▶ 2. RECON ─▶ 3. SCANNING/ENUM ─▶ 4. EXPLOITATION
     scope, RoE,          OSINT,      ports, services,      gain access
     authorization        DNS         vulns
                                                                │
  7. RETEST ◀── 6. REPORTING ◀── 5. POST-EXPLOITATION ◀─────────┘
     verify fixes   exec summary +    priv. escalation, lateral
                    technical detail  movement, data access (proof)
```

Methodologies: **PTES**, **OWASP WSTG** (web), **OSSTMM**, **NIST SP 800-115**.

### Q7.4 What should be in the scope / Rules of Engagement?

- Targets (IPs, URLs, apps, APIs) and **explicit exclusions**.
- Testing windows (avoid batch payment runs / peak hours), environment (prod vs staging).
- Allowed techniques (DoS usually excluded, social engineering yes/no).
- Credentials/accounts provided, test data.
- **Written authorization** (including from cloud/SaaS providers where their policy requires it).
- Emergency contacts, stop conditions, what to do on critical finding (immediate notification, not wait for report).
- Data handling (tester must not exfiltrate real customer data).
- Deliverables and retest included.

### Q7.5 How do you drive pentest findings to closure? (Job ad explicitly mentions "closing outstanding penetration test findings".)

```
  REPORT ──▶ TRIAGE ──▶ OWNERSHIP ──▶ PLAN ──▶ TRACK ──▶ VERIFY ──▶ CLOSE
             validate    named owner   fix or    SLA,      retest by    evidence
             severity,   per finding   accept    weekly    tester or    stored
             context     (not "IT")    w/ date   review,   internal     (audit)
             risk                                escalate
```

Practical tactics:
1. **Triage workshop** with app owner + tester: confirm validity, adjust severity with business context (environmental), agree root cause.
2. **Single tracking system** (Jira) — every finding a ticket with owner, tier, due date. No findings living only in PDFs.
3. **Group by root cause**: 15 findings from missing security headers → one platform change.
4. **Quick wins first** to build momentum; long fixes get interim mitigations.
5. **Regular cadence**: weekly/bi-weekly review; aging report visible to leadership.
6. **Escalation path**: overdue criticals → engineering manager → director → risk committee. Use data, not emotion.
7. **Retest** before closing — a ticket marked "done" is not "fixed".
8. **Feed back into design**: recurring finding types → secure coding training, SAST rule, reusable component, architecture standard.
9. For old backlog: do a **backlog reset** — re-validate old findings (some already fixed or systems decommissioned), then re-prioritize and commit to realistic dates.

Sound bite: *"A pentest's value is realized at closure, not at delivery of the report."*

### Q7.6 An app team disputes a pentest finding. What do you do?

- Listen: they may be right (false positive, existing control the tester didn't see).
- Ask for evidence; if needed, get the tester to demonstrate or clarify.
- If valid but disagreement on priority: discuss business impact, exploitability, and compensating controls — use the agreed prioritization model, not opinions.
- If they won't fix: formal risk acceptance by the **business owner** — makes the decision visible and owned. Usually, when people must sign a risk acceptance, priorities change.

### Q7.7 What makes a good pentest report?

Executive summary in business language; overall risk rating; each finding with: description, affected assets, **reproduction steps**, evidence, severity with CVSS vector and **contextual risk**, remediation guidance, references. Plus positive observations and a retest section.

---

## 8. Threat Detection, Monitoring & Deception

### Q8.1 SIEM vs SOAR vs EDR vs XDR vs NDR?

```
   SOURCES                    COLLECT & CORRELATE          RESPOND
  ┌───────────┐
  │ WAF/edge  │──┐
  │ Firewalls │──┤           ┌───────────────┐          ┌──────────────┐
  │ EDR       │──┼──logs────▶│    SIEM       │─alerts──▶│    SOAR      │
  │ IdP / SSO │──┤           │ (Splunk,      │          │ playbooks:   │
  │ Cloud     │──┤           │ Sentinel,     │          │ enrich, block│
  │ SaaS audit│──┤           │ QRadar,Elastic│          │ IP, disable  │
  │ Honeypots │──┘           │ correlation)  │          │ user, ticket │
  └───────────┘              └───────────────┘          └──────────────┘
   EDR = Endpoint Detection & Response (CrowdStrike, Defender for Endpoint)
   NDR = Network Detection & Response (Darktrace, Vectra, Zeek)
   XDR = Extended: integrated EDR + NDR + identity + cloud from one vendor
```

### Q8.2 What is MITRE ATT&CK and how do you use it?

A knowledge base of adversary **tactics** (why — the goal) and **techniques** (how). Enterprise matrix tactics:

```
  Recon → Resource Dev → Initial Access → Execution → Persistence →
  Privilege Escalation → Defense Evasion → Credential Access → Discovery →
  Lateral Movement → Collection → Command & Control → Exfiltration → Impact
```

Uses: map detections to techniques to find **coverage gaps**; threat-informed defense (prioritize techniques used by groups targeting finance, e.g., FIN7, Lazarus); structure red/purple team exercises; common language in reports.

Related: **Lockheed Martin Cyber Kill Chain** — 7 steps:
```
  Recon → Weaponize → Deliver → Exploit → Install → C2 → Actions on Objectives
  Mnemonic: "Real Warriors Don't Eat Ice Cream Alone"
```
Detect earlier in the chain = cheaper response.

### Q8.3 What is deception technology? What is a honeypot?

A **honeypot** is a decoy system with no legitimate business use, so **any interaction is suspicious by definition** → very high-fidelity alerts, near-zero false positives.

```
  ┌───────────────────────────── DECEPTION FAMILY ─────────────────────────────┐
  │ HONEYPOT     Decoy system/service (fake SSH, RDP, SMB, web admin, DB)     │
  │ HONEYNET     Network of honeypots                                         │
  │ HONEYTOKEN   Decoy data/credential: fake AWS key, fake DB record, fake    │
  │ / CANARY     admin account in AD, document with tracking beacon, fake API │
  │ TOKEN        key in a Git repo, fake card number (PAN) in a DB            │
  │ BREADCRUMB   Lures on real endpoints pointing to decoys (saved RDP        │
  │              connection, cached creds, fake mapped drive)                 │
  └────────────────────────────────────────────────────────────────────────────┘
```

**Interaction levels:**
```
  LOW-INTERACTION                          HIGH-INTERACTION
  emulated services (banner, login page)   real OS/services, fully usable
  + easy, safe, cheap                      + rich attacker intelligence (TTPs)
  – attacker may detect, little intel      – risky (can be used as pivot),
  e.g. OpenCanary, Cowrie (medium),          heavy to maintain & isolate
  T-Pot, Thinkst Canary
```

### Q8.4 Where would you place honeypots / honeytokens? (Architecture question.)

```
                 INTERNET
                    │
      [External honeypot]  ← threat intel on scanning/opportunistic attacks
                    │        (noisy! useful for intel/IOC feeds, NOT as alerts)
               ┌────▼────┐
               │   DMZ   │ ← decoy web admin panel / fake API endpoint
               └────┬────┘   (an attacker who got past the edge)
               ┌────▼──────────────────────┐
               │ INTERNAL                  │
               │  • fake file server (SMB) │ ← lateral movement detection
               │  • fake DB with honey PANs│ ← data theft detection
               │  • AD honey admin account │ ← credential abuse / kerberoasting
               │  • canary AWS keys in CI  │ ← secret theft detection
               │  • near EOL systems       │ ← early warning where risk is high
               └───────────────────────────┘
```

Key message: **internal deception gives the highest value** — detect an attacker who is *already inside* (post-initial access, during discovery & lateral movement), which traditional controls miss. Also **web deception**: fake hidden endpoints like `/admin-old` or fake parameters that only a scanner/attacker would touch → block that client at the WAF.

### Q8.5 How do honeypots feed monitoring and incident response? (Job ad wording!)

```
  Honeypot/token triggered
        │  (event: src IP, host, user, technique)
        ▼
  SIEM ── high-severity alert (no tuning needed – any hit is bad)
        │
        ▼
  SOAR playbook: enrich (asset owner, user, geo), isolate host via EDR,
        │        disable account, block IP at FW/WAF, open IR ticket
        ▼
  IR team investigates → scope compromise
        │
        ▼
  INTELLIGENCE LOOP: extract IOCs & TTPs (map to ATT&CK) →
    • new detection rules in SIEM/EDR
    • WAF/FW blocklists, hardening of the real systems attacked
    • update threat model & vuln priorities ("attackers target X")
```

That closing loop — detection → intelligence → **stronger controls** — is exactly what the job ad describes.

### Q8.6 Risks/considerations of deception?

- Honeypots must be **isolated** so they can't be used to pivot (especially high-interaction).
- Must look realistic (naming conventions, OS versions matching the estate).
- Must not contain real data.
- Keep an inventory so IT doesn't "fix" or scan them into noise; exclude from legitimate scanners, or tag scanner IPs.
- Legal: generally fine for detection on own networks; entrapment isn't a practical issue for defensive use; consider privacy (GDPR) of logged data.
- Alert routing & ownership defined before deployment — an unmonitored honeypot is useless.

### Q8.7 What are the phases of incident response?

**NIST SP 800-61** (classic Rev. 2):
```
  ┌─────────────┐   ┌──────────────────┐   ┌───────────────────────────┐   ┌──────────────┐
  │ PREPARATION │──▶│ DETECTION &      │──▶│ CONTAINMENT, ERADICATION  │──▶│ POST-INCIDENT│
  │             │   │ ANALYSIS         │◀──│ & RECOVERY                │   │ ACTIVITY     │
  └─────────────┘   └──────────────────┘   └───────────────────────────┘   └──────┬───────┘
         ▲                                                                        │
         └────────────────────── lessons learned ─────────────────────────────────┘
```
SANS version (6 steps): **PICERL** — Preparation, Identification, Containment, Eradication, Recovery, Lessons learned. (NIST 800-61 Rev. 3, 2025, realigns IR to CSF 2.0 functions.)

For a financial entity in the EU also mention **DORA major ICT incident reporting** timelines to regulators (initial notification within hours of classification).

### Q8.8 Which log sources are most important for detection?

Identity provider (sign-ins, MFA, risky logins), EDR, firewall/proxy/DNS, WAF/edge, cloud control plane (CloudTrail, Azure Activity), SaaS audit logs (M365, Salesforce, GitHub), database audit on sensitive tables, privileged access sessions, Kubernetes audit logs, application security events. **Log without detections = storage cost**; define use cases first.

---

## 9. Cloud & SaaS Security Assurance

### Q9.1 Explain the Shared Responsibility Model.

```
                    On-prem    IaaS       PaaS       SaaS
                   ┌────────┬──────────┬──────────┬──────────┐
  Data & access    │  YOU   │   YOU    │   YOU    │   YOU    │ ← ALWAYS yours
  Identities       │  YOU   │   YOU    │   YOU    │   YOU    │
  Application      │  YOU   │   YOU    │   YOU    │ provider │
  OS / runtime     │  YOU   │   YOU    │ provider │ provider │
  Network/virtual  │  YOU   │ shared   │ provider │ provider │
  Physical / DC    │  YOU   │ provider │ provider │ provider │
                   └────────┴──────────┴──────────┴──────────┘
```

Key point: **Data, identity, and configuration are always the customer's responsibility** — which is why most SaaS/cloud breaches are misconfiguration and credential abuse (e.g., 2024 Snowflake customer breaches — stolen credentials, no MFA).

### Q9.2 How would you design a SaaS / third-party security assurance process? (Core responsibility — practice!)

```
  ┌───────────┐  ┌───────────────┐  ┌──────────────┐  ┌────────────┐  ┌───────────┐  ┌──────────┐
  │1.INTAKE & │─▶│2.RISK TIERING │─▶│3.ASSESSMENT  │─▶│4.DECISION  │─▶│5.ONBOARD  │─▶│6.ONGOING │
  │ REGISTER  │  │ data class,   │  │ depth per    │  │ approve /  │  │ SSO, SCIM,│  │ periodic │
  │ business  │  │ criticality,  │  │ tier (quest.,│  │ approve w/ │  │ config    │  │ reviews, │
  │ owner,    │  │ integration,  │  │ certs, tech  │  │ conditions/│  │ baseline, │  │ SSPM,    │
  │ purpose   │  │ regulatory    │  │ review)      │  │ reject     │  │ logging   │  │ offboard │
  └───────────┘  └───────────────┘  └──────────────┘  └────────────┘  └───────────┘  └──────────┘
```

**Risk tiering** (drives effort — don't do a 300-question review for a diagramming tool):
```
  TIER 1 (Critical): customer/card data, PII, payment processing, critical
                     business function, privileged integration → full review,
                     contract clauses, annual reassessment, DORA register
  TIER 2 (High):     internal confidential data, integrations → standard review
  TIER 3 (Low):      no sensitive data, no integration → lightweight checklist
```

**Assessment evidence:**
- Certifications/reports: **SOC 2 Type II** (controls operated over a period — preferred over Type I which is point-in-time), **ISO/IEC 27001** certificate + **Statement of Applicability** (check scope covers the service you buy!), **PCI DSS AOC** if they handle card data, CSA STAR.
- Questionnaires: **SIG** (Shared Assessments), **CAIQ** (Cloud Security Alliance) — or your own tiered questionnaire.
- Pentest summary / letter of attestation, vulnerability management and incident notification process.
- Data residency (EU), sub-processors (fourth parties), encryption and key management, backup/DR, exit strategy.
- Legal/contract: DPA (GDPR), right to audit, breach notification timeline (e.g., 24–72h), security obligations, termination & data return/deletion.

**Onboarding requirements (security configuration):** SSO + MFA, SCIM, RBAC, audit log export to SIEM, data sharing settings, disable unnecessary integrations/public links, IP restrictions where available, API tokens scoped.

**Ongoing:** reassessment per tier, monitor vendor breach news, **SSPM** (SaaS Security Posture Management — e.g., AppOmni, Adaptive Shield, Obsidian) for configuration drift, review OAuth app grants (third-party apps connected to M365/Google), offboarding at contract end (revoke access, confirm data deletion).

### Q9.3 What would you check in a SaaS application technical review? (Four pillars from the job ad.)

```
  ┌──────────────────┬──────────────────────────────────────────────────────┐
  │ IDENTITY         │ SSO (SAML/OIDC) support, MFA enforcement, SCIM,      │
  │                  │ break-glass accounts, service accounts, session mgmt │
  ├──────────────────┼──────────────────────────────────────────────────────┤
  │ ACCESS           │ RBAC granularity, admin roles separated, least priv, │
  │                  │ access reviews, OAuth scopes of integrations, API    │
  │                  │ token management, tenant isolation                   │
  ├──────────────────┼──────────────────────────────────────────────────────┤
  │ CONFIGURATION    │ Secure defaults, public sharing disabled, IP allow-  │
  │                  │ lists, audit logging enabled & exported, password/   │
  │                  │ session policy, baseline (CIS benchmark if exists),  │
  │                  │ drift monitoring (SSPM)                              │
  ├──────────────────┼──────────────────────────────────────────────────────┤
  │ DATA PROTECTION  │ Encryption in transit (TLS1.2+) & at rest, key mgmt  │
  │                  │ (BYOK/HYOK for Tier 1), data residency, retention &  │
  │                  │ deletion, backup, DLP, data classification, sub-     │
  │                  │ processors, export/exit capability                   │
  └──────────────────┴──────────────────────────────────────────────────────┘
  Mnemonic: "I-A-C-D" → "I Always Check Data"
```

### Q9.4 A vendor fails some requirements but the business needs it urgently. What do you do?

- Clarify the gaps and the real risk (what data, what exposure).
- Look for **compensating controls**: restrict data types sent, CASB controls, limit users, stronger SSO conditional access, contractual commitments with dates.
- **Approve with conditions** + time-bound risk acceptance by the business owner + remediation commitments from the vendor tracked.
- If risk is unacceptable (e.g., card data without PCI compliance) — say no clearly, explain why, propose alternatives.
- Principle: *security enables the business decision; the business owner owns the risk, security makes it visible and advises.*

### Q9.5 What is CSPM, CWPP, CNAPP, CASB, SSPM?

```
  CSPM  Cloud Security Posture Mgmt  → misconfigurations in cloud accounts
                                       (public S3 bucket, open SG 0.0.0.0/0)
  CWPP  Cloud Workload Protection    → runtime protection for VMs/containers
  CIEM  Cloud Infra Entitlement Mgmt → excessive cloud IAM permissions
  CNAPP Cloud-Native App Protection  → CSPM + CWPP + CIEM + IaC scan unified
                                       (Wiz, Prisma Cloud, Defender for Cloud)
  CASB  Cloud Access Security Broker → visibility/control of SaaS usage,
                                       shadow IT, DLP (Netskope, Defender for
                                       Cloud Apps)
  SSPM  SaaS Security Posture Mgmt   → config posture INSIDE SaaS apps
```

### Q9.6 Top cloud security risks and controls?

- **Misconfiguration** (public storage, open security groups) → IaC with policy-as-code (OPA, Checkov), CSPM, guardrails (AWS SCPs / Azure Policy).
- **Excessive IAM permissions / long-lived keys** → roles with temporary credentials, workload identity (IRSA, Azure Managed Identity), CIEM, no root usage.
- **Exposed secrets** → secret managers, secret scanning in Git.
- **Lack of logging** → CloudTrail/Activity logs centralized, immutable.
- **Insecure APIs / metadata abuse** → IMDSv2, egress control.
- **Multi-account/landing zone** strategy: separate prod/non-prod, security tooling account, log archive account.

---

## 10. Cryptography & PKI Essentials

### Q10.1 Symmetric vs asymmetric vs hashing?

```
  ┌──────────────┬───────────────────────┬──────────────────────┬──────────────────┐
  │              │ SYMMETRIC             │ ASYMMETRIC           │ HASHING          │
  ├──────────────┼───────────────────────┼──────────────────────┼──────────────────┤
  │ Keys         │ one shared key        │ public + private     │ no key (HMAC     │
  │              │                       │                      │ uses a key)      │
  │ Speed        │ fast (bulk data)      │ slow                 │ fast             │
  │ Use          │ encrypt data          │ key exchange,        │ integrity,       │
  │              │                       │ signatures           │ passwords        │
  │ Examples     │ AES-256-GCM, ChaCha20 │ RSA-2048+, ECDSA,    │ SHA-256, SHA-3;  │
  │              │                       │ Ed25519, ECDH        │ bcrypt/Argon2 for│
  │              │                       │                      │ passwords        │
  │ Avoid        │ DES, 3DES, RC4, ECB   │ RSA-1024             │ MD5, SHA-1       │
  └──────────────┴───────────────────────┴──────────────────────┴──────────────────┘
  TLS uses all three: asymmetric to agree a key → symmetric for data → hash/MAC
  for integrity.
```

Passwords: never plain hash — use slow, salted algorithms (**Argon2id, bcrypt, scrypt, PBKDF2**).

### Q10.2 Digital signature — how does it work?

```
  SENDER: hash(message) ──encrypt with SENDER PRIVATE key──▶ signature
  RECEIVER: decrypt signature with SENDER PUBLIC key → hash1
            hash(received message) → hash2
            hash1 == hash2 ? → authentic + unmodified (integrity + non-repudiation)
```

### Q10.3 What is PKI and a certificate chain?

```
   ┌──────────────┐  signs  ┌──────────────────┐  signs  ┌──────────────────┐
   │ ROOT CA      │───────▶ │ INTERMEDIATE CA  │───────▶ │ LEAF / SERVER    │
   │ (offline,    │         │ (online, issues  │         │ CERT (api.bank.  │
   │ in trust     │         │ certificates)    │         │ com)             │
   │ stores)      │         └──────────────────┘         └──────────────────┘
   └──────────────┘
   Revocation: CRL, OCSP (+ OCSP stapling)
```

Architect concerns: **certificate lifecycle automation** (ACME, cert-manager) — public TLS certificate lifetimes are being reduced in stages toward 47 days by 2029 (CA/Browser Forum decision), so manual renewal won't scale; certificate inventory and expiry monitoring (outages from expired certs are common).

### Q10.4 Key management and HSMs — why important in payments?

- **HSM** (Hardware Security Module): tamper-resistant device for generating/storing keys and crypto operations; keys never leave in clear. Payment HSMs (Thales payShield, Utimaco) handle PIN translation, card verification, key blocks — required in card processing (PCI PIN, PCI HSM).
- Cloud: **KMS** (AWS KMS, Azure Key Vault) with envelope encryption; CloudHSM / Managed HSM for dedicated.
- Principles: key rotation, separation of duties (key custodians, split knowledge / dual control), key hierarchy (KEK encrypts DEK).
- **Tokenization vs encryption**: tokenization replaces PAN with a non-mathematical surrogate → systems holding only tokens can be out of PCI scope.

### Q10.5 What is post-quantum cryptography and should we care?

Quantum computers could break RSA/ECC (Shor's algorithm). NIST finalized PQC standards in Aug 2024: **ML-KEM** (FIPS 203, key encapsulation, from Kyber), **ML-DSA** (FIPS 204, signatures, from Dilithium), **SLH-DSA** (FIPS 205). Risk today: **"harvest now, decrypt later"** for long-lived sensitive data. Action: crypto inventory, **crypto-agility**, hybrid key exchange (e.g., X25519+ML-KEM already in modern browsers/TLS libraries). Mentioning this shows strategic awareness.

---

## 11. Frameworks, Standards & Regulation

### Q11.1 Which frameworks do you know and when do you use them?

```
  ┌────────────────┬───────────────────────────────────────────────────────────┐
  │ NIST CSF 2.0   │ Outcome framework, 6 functions (see below). Good for      │
  │ (Feb 2024)     │ maturity assessment & exec communication.                 │
  │ ISO/IEC 27001  │ Certifiable ISMS standard; Annex A has 93 controls in 4   │
  │ :2022          │ themes (Organizational 37, People 8, Physical 14,         │
  │                │ Technological 34). 27002 = control guidance.             │
  │ PCI DSS v4.0.1 │ Mandatory for card data; v4.0 future-dated requirements   │
  │                │ became mandatory 31 Mar 2025 (e.g., 6.4.3 payment page    │
  │                │ scripts, 11.6.1 change detection, 12.3.1 targeted risk    │
  │                │ analysis, MFA for all CDE access 8.4.2).                  │
  │ CIS Controls v8│ Prioritized 18 controls, Implementation Groups IG1–3.     │
  │ CIS Benchmarks │ Hardening configs for OS, cloud, K8s, DBs.                │
  │ OWASP ASVS     │ Application Security Verification Standard — use to       │
  │                │ define app security REQUIREMENTS (levels 1–3).            │
  │ OWASP SAMM     │ Software assurance maturity model.                        │
  │ SABSA / TOGAF  │ Enterprise (security) architecture methods — SABSA is     │
  │                │ business-driven, top-down (contextual→component layers).  │
  │ MITRE ATT&CK   │ Adversary behavior / detection coverage.                  │
  └────────────────┴───────────────────────────────────────────────────────────┘
```

**NIST CSF 2.0 functions:**
```
                         ┌──────────┐
                         │ GOVERN   │  (new in 2.0 — at the center)
                         └────┬─────┘
    ┌──────────┬──────────┬───┴──────┬──────────┬──────────┐
    │ IDENTIFY │ PROTECT  │ DETECT   │ RESPOND  │ RECOVER  │
    └──────────┴──────────┴──────────┴──────────┴──────────┘
   Mnemonic: "Good Individuals Protect, Detect, Respond, Recover"
   Map to this job: Vuln Mgmt/SaaS assessment = IDENTIFY; WAF/edge = PROTECT;
                    honeypots = DETECT; IR integration = RESPOND.
```

### Q11.2 What EU regulations matter for a fintech in Portugal?

- **DORA** (Digital Operational Resilience Act, Regulation (EU) 2022/2554) — **applies since 17 Jan 2025** to financial entities **and** creates oversight of critical ICT third-party providers. Five pillars:
```
  ┌─────────────────┬──────────────────┬─────────────────┬──────────────────┬────────────────┐
  │ ICT RISK MGMT   │ INCIDENT         │ RESILIENCE      │ ICT THIRD-PARTY  │ INFORMATION    │
  │ framework       │ REPORTING        │ TESTING         │ RISK (register   │ SHARING        │
  │                 │ (major incidents │ (incl. TLPT —   │ of information,  │ (threat intel) │
  │                 │ to regulators)   │ threat-led      │ contract clauses,│                │
  │                 │                  │ pentests)       │ exit strategies) │                │
  └─────────────────┴──────────────────┴─────────────────┴──────────────────┴────────────────┘
```
  As a service provider to financial institutions (e.g., Worldline), EGS will receive DORA-driven contractual requirements from clients — your SaaS/third-party assurance, vuln management and pentest work directly supports this. **This is a strong point to raise.**
- **NIS2** (Directive (EU) 2022/2555) — transposed nationally (Portugal via its own national law); broader essential/important entities; management accountability; 24h early warning / 72h incident notification.
- **GDPR** — personal data protection; 72h breach notification to the supervisory authority (in Portugal: CNPD); privacy by design.
- **PSD2 / upcoming PSD3 & PSR** — strong customer authentication (SCA), open banking APIs.
- **PCI DSS** — contractual (card brands), not law, but mandatory for card processing.
- **EU AI Act** — increasingly relevant if AI features are used.

### Q11.3 What is the difference between a policy, standard, procedure, and guideline?

```
  POLICY     ─ WHAT & WHY, high level, mandatory, approved by management
   │          "All internet-facing apps must be protected by edge controls."
  STANDARD   ─ specific mandatory requirements
   │          "WAF in block mode with managed OWASP rules; TLS 1.2+."
  PROCEDURE  ─ HOW, step by step
   │          "How to onboard an app to the WAF."
  GUIDELINE  ─ recommended, optional best practice
              "Tips for tuning false positives."
```

As an architect you mostly write **standards / security requirements** and **reference architectures**.

---

## 12. Secure SDLC, DevSecOps & Containers

### Q12.1 Where do security activities fit into the SDLC?

```
  PLAN/REQ     DESIGN         CODE          BUILD          TEST           DEPLOY         OPERATE
  ────────     ──────         ────          ─────          ────           ──────         ───────
  security     threat         IDE plugins,  SAST, SCA,     DAST, IAST,    IaC scan,      runtime
  reqs (ASVS), modeling,      secure coding secret scan,   pentest,       signed images, protection,
  abuse cases  arch review    peer review   SBOM, image    API fuzzing    admission      WAF, EDR,
                                            scan, signing                 control        monitoring,
                                                                                         vuln scan
  ◀────────────────────── "SHIFT LEFT" ──────────────────────   ─────── "SHIELD RIGHT" ──────▶
```

- **SAST**: static code analysis (SonarQube, Checkmarx, Semgrep, CodeQL).
- **SCA**: third-party dependencies/licenses (Snyk, Dependabot, OWASP Dependency-Check, Black Duck).
- **DAST**: running app, black-box (OWASP ZAP, Burp Suite).
- **IAST**: agent inside running app during tests (Contrast).
- **RASP**: runtime self-protection inside app.
- **Secrets scanning**: Gitleaks, TruffleHog, GitHub secret scanning.

### Q12.2 What is software supply chain security?

- **SBOM** (Software Bill of Materials — CycloneDX, SPDX): list of all components → answer "are we affected?" in minutes.
- Signed artifacts & provenance: **Sigstore/cosign**, **SLSA** levels.
- Pin dependencies, private artifact repository (Nexus/Artifactory) as a proxy, dependency confusion protection.
- Secure CI/CD: least-privilege runners, OIDC federation instead of static cloud keys, protected branches, required reviews.
- Examples: SolarWinds (2020), Codecov, 3CX, xz-utils backdoor (2024), malicious npm/PyPI packages.

### Q12.3 How do you secure containers and Kubernetes?

```
  IMAGE          minimal/distroless base, non-root user, scanned, signed,
                 no secrets baked in, rebuilt regularly
  REGISTRY       private, scan on push, only signed images deployable
  CLUSTER        RBAC least privilege, API server not public, etcd encrypted,
                 audit logs, CIS K8s benchmark (kube-bench), managed K8s patching
  ADMISSION      Pod Security Standards (restricted), OPA Gatekeeper / Kyverno
                 (no privileged, no hostPath, resource limits, allowed registries)
  NETWORK        NetworkPolicies default deny; service mesh mTLS
  SECRETS        external secrets (Vault, CSI driver) — K8s Secrets are only
                 base64 by default; enable encryption at rest
  RUNTIME        Falco / EDR for containers — detect shell in container, etc.
```

(You have hands-on Docker/K8s — speak from experience here.)

---

## 13. Risk Communication & Influencing

### Q13.1 How do you communicate technical risk to non-technical stakeholders?

Translate **vulnerability → business impact**. Use a structured risk statement:

```
  ┌──────────────────────────────────────────────────────────────────────┐
  │ "Because of  [CAUSE / vulnerability],                                │
  │  [THREAT]    could  [EVENT],                                         │
  │  resulting in  [BUSINESS IMPACT: € / customers / regulator / SLA]."  │
  │                                                                      │
  │ Example: "Because our merchant portal runs on an end-of-life server  │
  │ that can no longer be patched, an attacker exploiting a known flaw   │
  │ could access merchant settlement data, resulting in GDPR/DORA        │
  │ reportable breach, client contract penalties, and reputational       │
  │ damage with Worldline."                                              │
  └──────────────────────────────────────────────────────────────────────┘
```

Then give: **likelihood** (evidence: exploited in the wild? internet-facing?), **options** with cost/effort, and a **recommendation**. Executives want a decision, not a lecture.

**Tailor the message to the audience:**
```
  ┌────────────────┬──────────────────────────────────────────────────────┐
  │ Board / Execs  │ Business risk, trend, € impact, regulatory, decision │
  │                │ needed. 1 page. Heat map.                            │
  │ Product owners │ Impact on their product/customers, effort, deadline  │
  │ Engineers      │ Technical detail, reproduction, fix options, why it  │
  │                │ matters (respect their time & context)               │
  │ Vendors        │ Precise requirement, evidence expected, contract     │
  │                │ reference, deadline                                  │
  │ Auditors /     │ Control, evidence, process adherence                 │
  │ Regulators     │                                                      │
  └────────────────┴──────────────────────────────────────────────────────┘
```

### Q13.2 What is a risk heat map?

```
  IMPACT ▲
  Severe │  M     H     H     C     C
  Major  │  M     M     H     H     C
  Moder. │  L     M     M     H     H
  Minor  │  L     L     M     M     H
  Insig. │  L     L     L     M     M
         └──────────────────────────────▶ LIKELIHOOD
           Rare  Unlik Possib Likely Almost
                                     certain
  L=Low M=Medium H=High C=Critical   → drives treatment & escalation level
```

Caveat to show maturity: qualitative heat maps are subjective; for big decisions, quantitative methods like **FAIR** (Factor Analysis of Information Risk — loss event frequency × loss magnitude, in €) help compare with other business risks.

### Q13.3 How do you influence without authority? (Listed as a required attribute!)

```
  ┌──────────────────── INFLUENCE TOOLKIT ────────────────────┐
  │ 1. RELATIONSHIPS  build trust before you need it           │
  │ 2. DATA           metrics & evidence, not opinions         │
  │ 3. CONTEXT        link to THEIR goals (uptime, client      │
  │                   audits, delivery dates)                  │
  │ 4. EASY PATH      provide templates, reusable components,  │
  │                   pair with engineers                      │
  │ 5. PRIORITIZE     ask for the few things that matter —     │
  │                   credibility is a budget, don't waste it  │
  │ 6. GOVERNANCE     when needed: agreed standards, risk      │
  │                   acceptance signed by owners, escalation  │
  │ 7. RECOGNITION    celebrate teams that fix things publicly │
  └────────────────────────────────────────────────────────────┘
```

Your story here: as a Tech Lead **without formal HR authority**, you already drove architecture decisions and mentoring across teams — this is exactly influencing without authority. Prepare a concrete example.

### Q13.4 How do you balance security with delivery speed?

- Risk-based: not every finding blocks a release — define clear **release gates** only for high-risk issues (e.g., no new criticals, no secrets in code).
- Automate checks in pipelines so security is fast.
- Provide paved roads (secure templates).
- Use time-bound exceptions with owners rather than blocking everything.
- *"My goal is not zero risk — it's known, owned, and acceptable risk."*

---

## 14. Scenario / Architecture Questions

> Use this answer frame for every scenario — memorize it:
```
  ┌─────────┬─────────┬──────────┬──────────┬───────────┬────────────┐
  │CLARIFY  │ ASSESS  │ CONTAIN/ │ DECIDE   │ COMMUNI-  │ IMPROVE    │
  │scope,   │ risk,   │ QUICK    │ options, │ CATE      │ root cause,│
  │context  │ exposure│ WINS     │ owners   │ status    │ prevent    │
  └─────────┴─────────┴──────────┴──────────┴───────────┴────────────┘
  Mnemonic: "Can All Cats Dance Calmly Indoors?"
```

### S1. "Design the security architecture for a new public payment API."

```
  Merchant/App
      │ TLS 1.3, mTLS for B2B clients or OAuth2 client credentials
      │ (private_key_jwt), request signing for integrity
      ▼
  ┌─────────────────── EDGE ───────────────────┐
  │ DDoS │ WAF (OWASP + API schema validation) │
  │ Bot mgmt │ rate limit per client/key       │
  └──────────────────┬─────────────────────────┘
                     ▼
  ┌──────────── API GATEWAY ─────────────┐
  │ token validation, scopes, quotas,    │
  │ routing, request size limits         │
  └──────────────────┬───────────────────┘
                     ▼
  ┌──────── Payment services (K8s) ──────┐     ┌──────────────────────┐
  │ object-level authZ, idempotency keys,│────▶│ Tokenization / HSM   │
  │ input validation, mTLS mesh          │     │ (CDE, PCI scope)     │
  └──────────────────┬───────────────────┘     └──────────────────────┘
                     ▼
            DB (encrypted, least-priv accounts, audit)
  Cross-cutting: secrets vault, centralized logging → SIEM, fraud
  monitoring, SAST/SCA/DAST in CI, pentest before go-live, threat model,
  PCI DSS & DORA requirements mapped.
```

Mention: idempotency to prevent double charging, replay protection (nonce/timestamp), no PAN in logs, **BOLA** protection (merchant A can't query merchant B's transactions), versioning & deprecation of old API versions (inventory = OWASP API9).

### S2. "You join and find 200 open pentest findings, some older than 18 months. What do you do in the first 30 days?"

1. **Consolidate** into one tracker; remove duplicates.
2. **Re-validate** old items (quick rescan/retest) — close those fixed or on decommissioned systems.
3. **Re-prioritize** with the risk model (internet-facing + critical first).
4. **Assign named owners** with agreed dates; interim mitigations for the top items.
5. **Group by root cause** — likely a few systemic causes (headers, TLS config, outdated library, weak session handling).
6. **Reporting cadence**: weekly burn-down to leadership; escalate only with data.
7. **Prevent recurrence**: add checks to CI, secure baseline, include retest in every pentest contract, SLA policy.

### S3. "The WAF is blocking legitimate customer payments after a rule update. The business wants you to turn the WAF off."

- Don't turn it off globally. **Contain**: identify the specific rule (WAF logs → rule ID, path, parameter), create a **narrow exclusion** or switch *that rule* to log-only for that path — within minutes.
- Communicate impact & ETA to business.
- Root cause: why did an update reach prod without testing? → staged rollout (log mode first), canary, automated regression with real traffic samples.
- Document the exception with owner and expiry; re-enable after tuning.
- Shows: **calm under pressure**, pragmatic, but not compromising the whole control.

### S4. "A critical internet-facing system is End-of-Life and the owner says migration will take 12 months."

See Q6.5 flow: isolate/reduce exposure (put behind reverse proxy + WAF, remove direct internet access, IP allowlist for partners), virtual patching, enhanced monitoring + honeytokens around it, extended support if possible, formal time-bound risk acceptance signed by business owner, funded migration plan with milestones, monthly review. Ask: can we **accelerate** by moving only the public-facing part first?

### S5. "A business unit already bought a SaaS tool without security review and uploaded customer data."

- Don't shame — understand the need; shadow IT means the official process is too slow or unknown.
- Rapid assessment: what data, how many users, SSO/MFA? Vendor certifications?
- Immediate hardening: enable MFA/SSO, restrict sharing, audit logs.
- Complete assessment → approve with conditions or plan exit.
- Fix the process: publish a fast-track intake, CASB discovery for shadow SaaS, procurement integration (no PO without security review for Tier 1/2).

### S6. "How would you start deception technology from zero with limited budget?"

```
  Phase 1 (weeks)   Honeytokens: fake AD admin account, canary cloud keys in
                    repos/CI, honey records in key DBs, canary documents.
                    Open-source low-interaction honeypots (OpenCanary) in
                    internal server VLANs. Alerts → SIEM → IR runbook.
  Phase 2 (months)  Expand coverage to DMZ & near high-risk (EOL) systems;
                    web deception endpoints wired to WAF auto-block;
                    breadcrumbs on endpoints. Measure: coverage per segment,
                    time-to-detect in purple-team tests.
  Phase 3           Evaluate commercial platform (Thinkst Canary, Zscaler
                    Deception, Acalvio, SentinelOne Singularity Hologram);
                    threat intel from external honeypots; ATT&CK mapping.
```

### S7. "How would you assess whether our current edge deployment is secure?"

1. Gather: architecture diagrams, asset list, WAF/CDN configurations, logs.
2. Compare against the **edge requirements baseline** (Q3.4) → gap list.
3. Technical validation: external discovery (are there unprotected hostnames?), origin-bypass test, safe payload tests (blocked?), TLS scan (e.g., SSL Labs/testssl.sh), DDoS runbook check.
4. Review operations: who can change rules, change process, exception register, log flow into SIEM, alerting.
5. Output: prioritized findings with risk ratings, roadmap (quick wins / 90-day / 12-month), KPIs to track improvement.

### S8. "What would your first 90 days look like?"

```
  DAYS 1–30  LISTEN & LEARN
             Meet stakeholders (CISO, eng. leads, app owners, SOC, vendors,
             risk/compliance). Understand crown jewels, regulatory/client
             obligations (DORA, PCI, Worldline requirements). Review current
             VM data, pentest backlog, edge setup, SaaS inventory.
             Deliver: current-state assessment + top-10 risks.
  DAYS 31–60 PRIORITIZE & QUICK WINS
             Propose risk-based VM model & SLAs; fix obvious gaps (origin
             lock, KEV items, critical overdue pentest findings); draft edge
             & SaaS security standards; start honeytoken pilot.
  DAYS 61–90 ROADMAP & GOVERNANCE
             12-month roadmap with owners/budget; KPI dashboard; SaaS
             assessment process live; regular risk reporting cadence.
```

---

## 15. Behavioral Questions (STAR)

> **STAR** = Situation → Task → Action → Result. Spend most time on **Action** ("I", not "we") and end with a measurable **Result** + what you learned.
```
  S ──▶ T ──▶ A A A A ──▶ R (+ lesson)
  10%   10%     60%        20%
```
> The outlines below are templates — replace them with your real details, numbers, and names of systems before the interview.

### B1. "Tell me about a time you identified a security risk others had missed."
Template: While designing/reviewing [service, e.g. transfer microservice at OCS / a payment service], I noticed [e.g. services trusting internal network calls without authentication / sensitive data in logs / outdated library with known CVE]. Task: get it fixed without delaying release. Action: assessed exploitability, explained impact in business terms, proposed a low-effort fix (e.g., shared auth filter / log masking), implemented it as a reusable component. Result: fixed across N services; became team standard.

### B2. "Tell me about a time you influenced a decision without authority."
Template: As Technical Lead (no HR authority), I needed teams to adopt [e.g. a new architecture pattern / security control]. Action: built a proof of concept, gathered data (performance/incident numbers), addressed concerns one-on-one, found a champion. Result: adopted by X teams.

### B3. "Describe a time you had to make a decision with incomplete information / under pressure."
Template: Production incident on a financial platform (e.g., high-throughput accounting service). Action: stabilized first (containment), made a reversible decision, communicated clearly, then root-cause analysis. Result: restored in X minutes; introduced monitoring/process to prevent recurrence. Link: "Same approach applies to security incidents."

### B4. "Tell me about a disagreement with a stakeholder or vendor."
Template: vendor/team resisted a requirement (e.g., performance vs encryption, deadline vs fix). Action: understood their constraint, presented options with trade-offs, agreed interim mitigation + deadline. Result: requirement met, relationship preserved.

### B5. "Tell me about a mistake you made."
Choose a real, moderate mistake; own it; show what you changed (process, checklist, automation). Don't pick something catastrophic or a fake weakness.

### B6. "Why do you want to move from development to security architecture?"
> "Throughout my career the most impactful decisions I made were architectural — and in financial systems, security is architecture. I've seen that security teams often struggle because their requirements don't fit how systems are built, and engineering teams struggle because security arrives late as a list of findings. I want to sit exactly in that gap. My network foundation plus 13 years of building regulated platforms lets me define requirements that are both secure and deliverable."

### B7. "Why EGS / why this role in Porto?"
Connect to: fintech & payments focus you already know from inside EGS, strategic + hands-on nature of the role, the chance to build programs (VM, deception, SaaS assurance) rather than just operate tools. (Prepare your personal reason for Porto/relocation honestly.)

### B8. "How do you stay current in security?"
Examples: CISA KEV and advisories, OWASP projects, NIST publications, vendor security blogs, podcasts (e.g., Risky Business, Darknet Diaries), SANS reading room, practicing on labs (PortSwigger Web Security Academy, TryHackMe/HackTheBox), and planned certifications (see below).

**Certifications worth mentioning as plan** (shows commitment): **CISSP** or **CCSP** (architect/cloud), **CompTIA Security+** (foundation, quick win), **CCSK** (cloud), **AWS/Azure security specialty**, **SABSA Foundation**, **OSCP** later (hands-on offensive). Say which one you've started or will start.

---

## 16. Questions to Ask Them

Pick 4–5; they show architect-level thinking:

1. "How is the security function structured today — who owns SOC, IR, and GRC, and where does this role sit?"
2. "What does the vulnerability management program look like today — tooling, SLAs, and what's the biggest pain point: visibility, prioritization, or remediation?"
3. "How large is the backlog of pentest findings and EOL systems, and is there budget/leadership support to address them?"
4. "Which edge/WAF platforms are in use, and is configuration managed as code?"
5. "How are DORA and client requirements (e.g., from Worldline) influencing security priorities?"
6. "Is there an existing SaaS inventory and third-party risk process, or would I be building it?"
7. "What would success look like for this role after 6 and 12 months?"
8. "How are security exceptions and risk acceptances governed today?"
9. "Is deception technology already in place or would this be greenfield?"

---

## 17. Final Memory Cheat Sheet

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                        SECURITY ARCHITECT — ONE PAGE                         │
├──────────────────────────────────────────────────────────────────────────────┤
│ CIA + AAA          Confidentiality Integrity Availability | AuthN AuthZ Audit│
│ STRIDE             Spoof Tamper Repudiate InfoDisclose DoS Elevate          │
│ Risk               Likelihood × Impact; treat: Treat/Transfer/Tolerate/Term. │
│ Zero Trust         Verify explicitly · Least privilege · Assume breach       │
├──────────────────────────────────────────────────────────────────────────────┤
│ EDGE               Anycast/CDN → DDoS → WAF → Bot → Rate limit → ORIGIN LOCK │
│ WAF rollout        Discover → Log mode → Tune → Block → Review               │
│ WAF models         Negative (blocklist) · Positive (allowlist/schema)        │
│ Virtual patch      WAF/IPS rule = compensating control, not a fix            │
├──────────────────────────────────────────────────────────────────────────────┤
│ VULN PRIORITY      CVSS (how bad) + EPSS (how likely) + KEV (exploited now)  │
│                    + Exposure (internet?) + Asset criticality                │
│ Tiers/SLA          P0 24–72h · P1 7–14d · P2 30d · P3 90d · P4 best effort   │
│ EOL                Isolate → Virtual patch → Monitor → Risk accept (expiry)  │
│                    → Funded migration                                        │
│ KPIs               % in SLA · MTTR · coverage · KEV open · aging · EOL trend │
├──────────────────────────────────────────────────────────────────────────────┤
│ PENTEST CLOSURE    Triage → Owner → Plan → Track → Retest → Close (evidence) │
│ Types              Scan · Pentest · Red · Purple · Bug bounty                │
├──────────────────────────────────────────────────────────────────────────────┤
│ DECEPTION          Honeypot · Honeytoken · Breadcrumb; any hit = real alert  │
│ Loop               Detect → SIEM → SOAR → IR → Intel → Stronger controls     │
│ Kill Chain         Recon Weapon Deliver Exploit Install C2 Actions           │
│ IR (NIST)          Prepare → Detect/Analyze → Contain/Eradicate/Recover → PIR│
├──────────────────────────────────────────────────────────────────────────────┤
│ SaaS ASSURANCE     Intake → Tier → Assess → Decide → Onboard → Ongoing       │
│ Review pillars     Identity · Access · Configuration · Data  ("I Always     │
│                    Check Data")                                              │
│ Evidence           SOC 2 Type II · ISO 27001 + SoA scope · PCI AOC · pentest │
│ Shared resp.       Data, identity, config = ALWAYS customer                  │
├──────────────────────────────────────────────────────────────────────────────┤
│ REGULATION         DORA (since Jan 2025) · NIS2 · GDPR (72h) · PCI DSS 4.0.1 │
│ Frameworks         NIST CSF 2.0 (Govern+IPDRR) · ISO 27001:2022 · CIS · ASVS │
├──────────────────────────────────────────────────────────────────────────────┤
│ SCENARIO FRAME     Clarify → Assess → Contain → Decide → Communicate→Improve │
│ RISK STATEMENT     Because [cause], [threat] could [event] → [business impact]│
│ SOUND BITES        "CVSS is severity, not risk."                             │
│                    "Make the secure path the easy path."                     │
│                    "Known, owned, acceptable risk — not zero risk."          │
│                    "A pentest's value is realized at closure."               │
└──────────────────────────────────────────────────────────────────────────────┘
```

**Good luck, Meysam!** Practice sections 3, 6, 7, 8, 9 and 14 out loud — those map directly to the job's four responsibility areas.
