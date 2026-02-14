# System Architecture

> Technical architecture documentation for Top Crawler.
> Follows the [C4 Model](https://c4model.com/) with Mermaid.js diagrams.

---

## 1. System Context (C4 Level 1)

How Top Crawler fits into the broader ecosystem. The system operates between the user and target websites, with multiple privacy and intelligence layers mediating every interaction.

```mermaid
C4Context
    title System Context — Top Crawler

    Person(operator, "Operator", "Security researcher, penetration tester, or web developer")

    System(crawler, "Top Crawler", "AI-powered undetectable web crawler with full-spectrum privacy")

    System_Ext(targets, "Target Websites", "Protected by Cloudflare, DataDome, PerimeterX, Akamai, custom WAFs")
    System_Ext(vpn, "VPN Providers", "Encrypted tunnel — 3 provider integrations")
    System_Ext(tor, "Tor Network", "Onion routing with circuit rotation")
    System_Ext(proxies, "Proxy Network", "Residential and datacenter IP rotation")
    System_Ext(llm, "Local LLM", "On-device language model for real-time monitoring")
    System_Ext(doh, "DoH Resolvers", "Encrypted DNS resolution")

    Rel(operator, crawler, "Configures and monitors", "CLI / Web Dashboard")
    Rel(crawler, targets, "Crawls", "HTTPS (stealth)")
    Rel(crawler, vpn, "Routes through", "Encrypted tunnel")
    Rel(crawler, tor, "Anonymizes via", "SOCKS5")
    Rel(crawler, proxies, "Rotates through", "HTTP/SOCKS5")
    Rel(crawler, llm, "Monitors with", "Local API")
    Rel(crawler, doh, "Resolves via", "HTTPS")
```

---

## 2. Container Architecture (C4 Level 2)

The major containers that compose the system. Each container is an independently testable component.

```mermaid
C4Container
    title Container Diagram — Top Crawler

    Person(operator, "Operator")

    Container_Boundary(ui, "User Interfaces") {
        Container(dashboard, "Web Dashboard", "Real-time streaming UI", "Live crawl monitoring, configuration, and control via SSE")
        Container(cli, "CLI Interface", "Interactive terminal", "Direct crawl execution with 147 configuration parameters")
    }

    Container_Boundary(engine, "Crawl Engine") {
        Container(orchestrator, "Crawl Orchestrator", "Async concurrent engine", "URL queue management, worker pool, rate control, circuit breaking")
        Container(processor, "Page Processor", "Content extraction", "HTML parsing, URL discovery, content validation, auto-retry")
        Container(browser, "Stealth Browser", "Headless with fingerprint injection", "JavaScript rendering, fingerprint profiles, human behavior simulation")
    }

    Container_Boundary(intelligence, "AI Intelligence") {
        Container(monitor, "AI Monitor", "LLM-powered analysis", "Real-time health monitoring, tiered alerts, adaptive evasion, per-domain learning")
        Container(optimizer, "Fingerprint Optimizer", "AI-driven selection", "Analyzes target defenses, selects optimal browser persona")
    }

    Container_Boundary(privacy, "Privacy Chain") {
        Container(identity, "Identity Firewall", "Session isolation", "Prevents authenticated data leaking to anonymous operations")
        Container(network, "Network Anonymizer", "Multi-layer encryption", "VPN → Tor → Proxy chaining, DNS-over-HTTPS, kill switch")
        Container(scrubber, "Metadata Scrubber", "Artifact sanitization", "Strips GPS, author, timestamps from all downloads")
    }

    Container_Boundary(data, "Data & Export") {
        Container(storage, "Crawl Database", "SQL + Full-Text Search", "Page storage, crawl history, FTS5 search, checkpoint persistence")
        Container(vector, "Vector Database", "Semantic search", "Embedding-based search across crawled content")
        Container(exporter, "Export Pipeline", "Multi-format output", "HTML mirror, Markdown, knowledge base vault, media downloads")
        Container(media, "Media Pipeline", "Streaming support", "HLS/DASH/video/audio/PDF/subtitles with quality selection")
    }

    Rel(operator, dashboard, "Monitors", "HTTP/SSE")
    Rel(operator, cli, "Executes", "Terminal")
    Rel(dashboard, orchestrator, "Controls")
    Rel(cli, orchestrator, "Controls")
    Rel(orchestrator, processor, "Dispatches pages")
    Rel(orchestrator, browser, "Renders JS pages")
    Rel(orchestrator, monitor, "Streams metrics")
    Rel(monitor, orchestrator, "Adjusts strategy")
    Rel(optimizer, browser, "Selects profile")
    Rel(browser, network, "Routes requests")
    Rel(network, identity, "Enforces isolation")
    Rel(processor, storage, "Stores content")
    Rel(processor, vector, "Indexes embeddings")
    Rel(processor, exporter, "Exports content")
    Rel(processor, media, "Downloads media")
    Rel(exporter, scrubber, "Sanitizes output")
    Rel(media, scrubber, "Sanitizes output")
```

---

## 3. Key Design Decisions

| Decision | Choice | Why | Alternatives Considered |
|----------|--------|-----|------------------------|
| **Invisibility Hierarchy** | 4-level defensive posture (invisible → indistinguishable → untraceable → minimal) | Single design principle that governs every feature. Ensures graceful degradation — if Level 1 fails, Level 2 catches it. | Ad-hoc stealth features (inconsistent coverage) |
| **AI-driven monitoring** | Local LLM with real-time analysis | Autonomous operation without manual oversight. Learns per-domain strategies. No cloud dependency. | Rule-based alerts (less adaptive), cloud LLM (privacy violation) |
| **Multi-layer network privacy** | VPN → Tor → Proxy chaining with DoH | No single point of failure. ISP sees VPN. VPN sees Tor entry. Tor sees proxy. Proxy sees target. Nobody sees the full picture. | VPN only (single point of trust), Tor only (slow, exit node risk) |
| **Stealth profiles** | 3 presets — authenticated, anonymous slow, anonymous fast | Matches crawl posture to target sensitivity. Authenticated mode preserves identity. Anonymous modes enforce identity firewall. | Single mode (inflexible), per-parameter tuning (complex) |
| **Async concurrent engine** | Fully async with connection pooling | 5-10x throughput over synchronous crawling. Connection reuse reduces TCP handshake overhead and fingerprinting surface. | Thread pool (GIL limited), multiprocessing (memory heavy) |
| **Circuit breaker** | Per-domain failure tracking with state machine | Prevents hammering failing domains. Auto-recovery after cooldown. Protects both crawler and target. | Global rate limiting (too coarse), no limiting (aggressive) |
| **Privacy-first defaults** | All external APIs disabled, local-only by default | Users opt-in to external services. Prevents accidental data leakage. Aligns with Invisibility Hierarchy Level 4. | External-first with opt-out (risky default) |

---

## 4. Data Flow — Primary Crawl Pipeline

```mermaid
flowchart TD
    A[Operator configures crawl] --> B[Fingerprint Optimizer analyzes target]
    B --> C[Stealth profile selected]
    C --> D[Network chain established<br/>VPN → Tor → Proxy → DoH]
    D --> E{URL Queue}

    E --> F[Fetch page through<br/>stealth browser]
    F --> G{Response Analysis}

    G -->|Real content| H[Extract content + URLs]
    G -->|Challenge/CAPTCHA| I[AI Monitor: escalate<br/>Invisibility Hierarchy]
    G -->|Block detected| J[AI Monitor: rotate identity<br/>+ network path]

    I --> K[Adjust fingerprint +<br/>behavior + network]
    J --> K
    K --> F

    H --> L[Store in database +<br/>index for search]
    H --> M[Export: HTML / Markdown /<br/>vault / vector]
    H --> N[Download media:<br/>video / audio / PDF]

    L --> O[Metadata scrubber]
    M --> O
    N --> O

    O --> P[Clean output to operator]

    H --> Q{More URLs?}
    Q -->|Yes| E
    Q -->|No| R[Crawl complete<br/>AI summary + scorecard]

    style I fill:#ff6b6b,color:#fff
    style J fill:#ff6b6b,color:#fff
    style K fill:#ffd93d,color:#333
    style O fill:#6bcb77,color:#fff
```

---

## 5. Security Posture

| Concern | Approach |
|---------|----------|
| **Operator Identity** | Identity firewall enforces complete session isolation between authenticated and anonymous modes. No session data, cookies, or browser storage leaks between modes. |
| **Network Privacy** | Multi-layer encryption chain: VPN tunnel → onion routing → proxy rotation. DNS encrypted via DoH. Kill switch halts all traffic if VPN drops. |
| **Data at Rest** | Metadata scrubbed from all downloaded artifacts (GPS, author, timestamps, EXIF). No operator PII in any output file, log, or database entry. |
| **Data in Transit** | All connections encrypted. TLS fingerprint matches claimed browser identity. No plaintext DNS queries. |
| **Input Validation** | URL sanitization, path traversal prevention, SSRF protection. Content validation with injection detection. |
| **Supply Chain** | Static analysis scanning, dependency vulnerability auditing, pre-commit security hooks. Dependency pinning with hash verification. |
| **Secrets** | API keys, tokens, and credentials never appear in logs. Sensitive data auto-redacted from all output. Secure memory handling for credentials. |
| **Detection Response** | AI monitor detects challenges, blocks, and degraded content. Automatically escalates through Invisibility Hierarchy levels. Never reveals automation under pressure. |

---

## 6. Technology Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Language | Python 3.14+ | Primary implementation — async/await native |
| Async Engine | Concurrent with connection pooling | High-throughput crawling with rate control |
| Browser Automation | Headless with stealth patches | JavaScript rendering, fingerprint injection, human simulation |
| AI/LLM | Local language model (on-device) | Real-time monitoring, adaptive evasion, domain learning |
| Database | Embedded SQL + FTS5 | Page storage, crawl history, full-text search |
| Vector Search | Embedding model + vector store | Semantic search across crawled content |
| Web Dashboard | Server-sent events streaming | Real-time crawl monitoring and control |
| Network Privacy | VPN + Tor + proxy + DoH | Multi-layer anonymization chain |
| Media Processing | Streaming protocol handlers | HLS, DASH, video, audio, PDF, subtitles |
| Security Scanning | Static analysis + dependency audit | Pre-commit and CI security gates |
| URL Deduplication | Probabilistic data structure | Memory-efficient seen-URL tracking |
| Resilience | Circuit breaker state machine | Per-domain failure detection and recovery |

---

## 7. Invisibility Hierarchy — Layer Application

How the 4-level hierarchy applies across every system layer:

```mermaid
flowchart LR
    subgraph L1["Level 1: Invisible"]
        A1[Match real browser TLS]
        A2[Match HTTP/2 framing]
        A3[Pass all fingerprint tests]
        A4[Human-realistic timing]
        A5[Encrypted DNS]
    end

    subgraph L2["Level 2: Indistinguishable"]
        B1[Consistent persona per session]
        B2[Geographic consistency]
        B3[Cookie jar warming]
        B4[Referrer chain simulation]
        B5[Natural resource loading]
    end

    subgraph L3["Level 3: Untraceable"]
        C1[VPN → Tor chaining]
        C2[Residential proxy rotation]
        C3[Fingerprint rotation between sessions]
        C4[No patterns linking sessions]
    end

    subgraph L4["Level 4: Minimal Exposure"]
        D1[Only required headers sent]
        D2[Block tracking pixels]
        D3[No unnecessary API calls]
        D4[Metadata scrubbed from output]
    end

    L1 -->|If detected| L2
    L2 -->|If identified| L3
    L3 ---|Always active| L4

    style L1 fill:#2d6a4f,color:#fff
    style L2 fill:#40916c,color:#fff
    style L3 fill:#52b788,color:#fff
    style L4 fill:#74c69d,color:#333
```

---

*This document describes the architectural design of Top Crawler.*
*Copyright 2026 TJ Neary. All Rights Reserved.*
