<!-- Hero image placeholder — generate with ChatGPT prompt below -->
<!-- ![Top Crawler](assets/social-preview.png) -->

![Showcase](https://img.shields.io/badge/type-showcase-purple?style=flat-square)
![Python](https://img.shields.io/badge/python-3.14+-blue?style=flat-square)
![Platform](https://img.shields.io/badge/platform-macOS%20%7C%20Linux-lightgrey?style=flat-square)
![License](https://img.shields.io/badge/license-All%20Rights%20Reserved-red?style=flat-square)

# Top Crawler

**An undetectable, AI-powered web crawler that defeats modern anti-bot systems at every layer.**

Built to prove a point: current bot detection technology has limits. Top Crawler demonstrates that a well-engineered system can pass TLS fingerprinting, HTTP/2 analysis, browser fingerprinting, behavioral analysis, and network-level detection — simultaneously — while leaving zero trace back to the operator.

Designed for security researchers, penetration testers, and web developers who need to understand the real capabilities and limitations of anti-bot defenses.

---

## The Invisibility Hierarchy

Every design decision follows a strict 4-level defensive posture, applied in priority order:

| Level | Principle | What It Means |
|-------|-----------|---------------|
| **1. Invisible** | No detection at all | The crawler leaves zero trace. No fingerprint anomalies, no timing artifacts, no network signatures. |
| **2. Indistinguishable** | Appears as a normal human | If detection occurs, the system presents a consistent, realistic human persona. Never identified as a crawler. |
| **3. Untraceable** | Nothing links to the operator | If identified as automated, no identity, location, or fingerprint connects back to the real operator. |
| **4. Minimal Exposure** | Reveal only what's necessary | At all times, only the minimum required information is transmitted. No voluntary data leakage. |

This hierarchy governs every layer — from TLS handshakes to DNS resolution to behavioral simulation.

---

## Architecture Highlights

### 1. Five-Layer Anti-Detection

The system addresses bot detection at every layer where modern anti-bot platforms operate:

| Layer | What Anti-Bot Systems Check | What Top Crawler Does |
|-------|----------------------------|----------------------|
| **TLS** | Cipher suite order, extensions, handshake patterns | Matches real browser signatures exactly |
| **HTTP/2** | SETTINGS frames, header ordering, stream priorities | Reproduces authentic browser protocol behavior |
| **Browser Fingerprint** | Canvas, WebGL, audio, fonts, plugins, screen, hardware | Generates internally consistent, hardware-realistic personas |
| **Behavioral** | Mouse movement, scroll patterns, typing cadence, timing | Simulates natural human interaction patterns |
| **Network** | IP reputation, DNS queries, connection patterns | Full encryption chain with residential IP support |

### 2. AI-Powered Adaptive Evasion

An integrated AI monitor observes crawl health in real-time and autonomously adjusts strategy:

- **Detection Response:** When blocking signals appear (CAPTCHAs, soft blocks, degraded content), the AI automatically escalates through the Invisibility Hierarchy — rotating identity, switching network paths, adjusting behavior patterns
- **Per-Domain Learning:** The system remembers what works for each target domain across sessions
- **Tiered Alerts:** Critical issues trigger automatic halt. Warnings trigger strategy adjustment. All decisions are logged with reasoning.

### 3. Full-Spectrum Privacy Chain

Every network layer is encrypted and anonymized. No single point of failure exposes the operator:

```
Application Layer    → Metadata scrubbed from all artifacts
     ↓
Browser Layer        → Identity firewall isolates sessions
     ↓
DNS Layer            → Encrypted resolution (DNS-over-HTTPS)
     ↓
Transport Layer      → VPN tunnel (3 provider integrations)
     ↓
Anonymity Layer      → Onion routing with circuit rotation
     ↓
Target Site          → Sees only a normal browser from a residential IP
```

---

## Capability Overview

| Capability | Details | Value |
|------------|---------|-------|
| **Anti-Detection** | Defeats Cloudflare, DataDome, PerimeterX, Akamai Bot Manager, and custom WAF rules | Access protected content that blocks conventional crawlers |
| **Stealth Profiles** | 3 preset identities — authenticated, anonymous slow, anonymous fast | Match crawl approach to target sensitivity |
| **Identity Firewall** | Complete session isolation between authenticated and anonymous operations | Operator identity never leaks to anonymous crawls |
| **AI Monitoring** | Real-time LLM analysis with tiered emergency alerts and auto-halt | Autonomous operation without manual oversight |
| **Fingerprint Optimization** | AI analyzes target domain defenses and selects optimal browser persona | Automatic adaptation to each site's detection stack |
| **Media Pipeline** | Video (HLS/DASH/streaming), audio, PDF, subtitles with quality selection | Complete site archival including multimedia |
| **Export Formats** | HTML mirror, Markdown, knowledge base vault, vector database for semantic search | Content accessible in any downstream workflow |
| **URL Scoping** | 6 constraint modes — from single page to full subdomain traversal | Precision control over crawl boundaries |
| **Resumable Crawls** | Checkpoint persistence with full state recovery | Long-running crawls survive interruptions |
| **Circuit Breaker** | Per-domain failure detection with automatic backoff and recovery | Self-healing operation across unreliable targets |
| **Metadata Scrubbing** | GPS, author, timestamps stripped from all downloaded artifacts | No operator fingerprints in saved content |
| **Privacy-First Defaults** | All external APIs disabled by default — everything runs locally | Zero data leaves the machine unless explicitly enabled |

---

## Metrics

| Metric | Value |
|--------|-------|
| Anti-detection layers | 5 (TLS, HTTP/2, fingerprint, behavioral, network) |
| Invisibility Hierarchy levels | 4 (invisible → indistinguishable → untraceable → minimal) |
| Stealth profiles | 3 (authenticated, anonymous slow, anonymous fast) |
| Crawl presets | 4 (video grabber, PDF collector, markdown archive, full archive) |
| URL constraint modes | 6 (none, host, host+1, subdomains, directory, custom) |
| VPN provider integrations | 3 (with kill switch and leak verification) |
| Configuration parameters | 147 (dataclass-based, CLI-configurable) |
| Export formats | 4 (HTML, Markdown, knowledge base vault, vector search) |
| Media support | HLS, DASH, streaming video, audio, PDF, subtitles (VTT/SRT) |
| Development phases | 13 planned (6 complete, 7 in roadmap) |
| Architecture | Fully async, concurrent with rate limiting and circuit breaking |
| AI integration | Local LLM monitoring with real-time adaptive optimization |

---

## Anti-Detection Benchmark Targets

Top Crawler is tested against the industry's detection platforms:

| Target | Category | Goal |
|--------|----------|------|
| Cloudflare Bot Management | Commercial CDN-level detection | Pass without challenge |
| DataDome | E-commerce bot protection | Pass without block |
| PerimeterX / HUMAN | Enterprise behavioral analysis | Pass without block |
| Akamai Bot Manager | CDN fingerprinting + anomaly detection | Pass without block |
| CreepJS | Open-source fingerprint analysis | < 30% detection score |
| BrowserLeaks | Canvas, WebGL, WebRTC, fonts, audio | All checks green |
| TLS fingerprint verification | JA3/JA4 hash matching | Match real browser exactly |
| HTTP/2 fingerprint verification | SETTINGS, headers, priority | Match real browser exactly |
| DNS/IP leak tests | WebRTC, DNS, IPv6 leak detection | Zero leaks |
| CAPTCHA trigger rate | Across 100+ page crawls on protected sites | < 5% trigger rate |

---

## CompTIA Relevance

This project maps directly to CompTIA Network+ and Security+ exam objectives:

| Concept | Net+ Coverage | Sec+ Coverage |
|---------|--------------|---------------|
| TLS/SSL and cipher suites | Protocol analysis, certificate chains | Cryptographic concepts, PKI |
| DNS resolution and encryption | DNS record types, name resolution | DNS attacks, privacy controls |
| VPN and tunneling | VPN protocols, network topologies | Anonymization, VPN security |
| Browser fingerprinting | HTTP headers, cookies, sessions | Client-side attacks, tracking |
| Rate limiting and evasion | Bandwidth management, QoS | DoS/DDoS, throttling concepts |
| Circuit breaker patterns | Connection management, fault tolerance | Availability, resilience |
| Metadata and forensics | File formats, EXIF data | Data sanitization, forensic analysis |
| Network anonymization | Proxy types, NAT, routing | IP spoofing, network attacks |

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Language | Python 3.14+ |
| Async Engine | Fully concurrent with connection pooling and rate control |
| Browser Automation | Headless browser with stealth patches and fingerprint injection |
| AI/LLM | Local language model integration for real-time monitoring |
| Database | Embedded SQL with full-text search (FTS5) |
| Vector Search | Embedding-based semantic search across crawled content |
| Web Dashboard | Real-time streaming UI with server-sent events |
| Network Privacy | Onion routing, VPN tunneling, encrypted DNS, proxy rotation |
| Media Processing | Streaming protocol support, subtitle extraction, transcription |
| Security | Static analysis, dependency auditing, pre-commit scanning |

---

## System Design

See [ARCHITECTURE.md](ARCHITECTURE.md) for the full system design with C4 diagrams covering:

- System context and external integrations
- Container architecture and component relationships
- Primary data flow through the crawl pipeline
- Security posture and privacy chain design
- Key architectural decisions with rationale

---

## Status

Active development. 6 phases complete, 7 more planned. Currently in the anti-detection evolution phase — adding TLS fingerprinting, HTTP/2 matching, timing defenses, and AI-driven adaptive evasion.

Near-term target: Live demonstration for CompTIA Net+/Sec+ class.

---

Copyright 2026 TJ Neary. All Rights Reserved.
