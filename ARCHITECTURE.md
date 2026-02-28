# System Architecture

> Technical architecture documentation for Top Crawler.
> Follows the [C4 Model](https://c4model.com/) with Mermaid.js diagrams.

---

## 1. System Context (C4 Level 1)

How Top Crawler fits into the broader ecosystem.

```mermaid
C4Context
    title System Context — Top Crawler

    Person(operator, "Operator", "Researcher or developer collecting web data")

    System(crawler, "Top Crawler", "LLM-powered web crawler with real-time monitoring and privacy controls")

    System_Ext(targets, "Target Websites", "Public web content sources")
    System_Ext(llm, "Local LLM", "On-device language model for real-time monitoring via Ollama")

    Rel(operator, crawler, "Configures and monitors", "CLI / Web Dashboard")
    Rel(crawler, targets, "Crawls", "HTTPS")
    Rel(crawler, llm, "Monitors with", "Local API")
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
        Container(browser, "Browser Renderer", "Headless browser", "JavaScript rendering for dynamic content")
    }

    Container_Boundary(intelligence, "AI Intelligence") {
        Container(monitor, "AI Monitor", "LLM-powered analysis", "Real-time health monitoring, tiered alerts, per-domain learning")
        Container(optimizer, "Config Optimizer", "AI-driven selection", "Analyzes target sites, selects optimal crawler configuration")
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
    Rel(optimizer, browser, "Selects config")
    Rel(processor, storage, "Stores content")
    Rel(processor, vector, "Indexes embeddings")
    Rel(processor, exporter, "Exports content")
    Rel(processor, media, "Downloads media")
```

---

## 3. Key Design Decisions

| Decision | Choice | Why | Alternatives Considered |
|----------|--------|-----|------------------------|
| **AI-driven monitoring** | Local LLM with real-time analysis | Autonomous operation without manual oversight. Learns per-domain strategies. No cloud dependency. | Rule-based alerts (less adaptive), cloud LLM (privacy concern) |
| **Async concurrent engine** | Fully async with connection pooling | 5-10x throughput over synchronous crawling. Connection reuse reduces overhead. | Thread pool (GIL limited), multiprocessing (memory heavy) |
| **Circuit breaker** | Per-domain failure tracking with state machine | Prevents hammering failing domains. Auto-recovery after cooldown. Respects target servers. | Global rate limiting (too coarse), no limiting (aggressive) |
| **Privacy-first defaults** | All external APIs disabled, local-only by default | Users opt-in to external services. Prevents accidental data leakage. | External-first with opt-out (risky default) |
| **Multi-format export** | HTML, Markdown, vault, vector | Content accessible in any downstream workflow — from static archives to AI-powered search. | Single format (inflexible) |
| **Resumable crawls** | Checkpoint persistence with full state recovery | Long-running crawls survive interruptions without data loss. | Start-from-scratch (wasteful) |

---

## 4. Data Flow — Primary Crawl Pipeline

```mermaid
flowchart TD
    A[Operator configures crawl] --> B[Config Optimizer analyzes target]
    B --> C[Optimal settings selected]
    C --> D{URL Queue}

    D --> E[Fetch page]
    E --> F{Response Analysis}

    F -->|Content received| G[Extract content + URLs]
    F -->|Error/block| H[AI Monitor: adjust strategy]

    H --> E

    G --> I[Store in database + index for search]
    G --> J[Export: HTML / Markdown / vault / vector]
    G --> K[Download media: video / audio / PDF]

    G --> L{More URLs?}
    L -->|Yes| D
    L -->|No| M[Crawl complete — AI summary + scorecard]

    style H fill:#ffd93d,color:#333
```

---

## 5. Technology Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Language | Python 3.14+ | Primary implementation — async/await native |
| Async Engine | Concurrent with connection pooling | High-throughput crawling with rate control |
| Browser | Playwright (headless) | JavaScript rendering for dynamic content |
| AI/LLM | Local language model via Ollama | Real-time monitoring, domain learning |
| Database | SQLite + FTS5 | Page storage, crawl history, full-text search |
| Vector Search | Embedding model + vector store | Semantic search across crawled content |
| Dashboard | Server-sent events streaming | Real-time crawl monitoring and control |
| Media | Streaming protocol handlers | HLS, DASH, video, audio, PDF, subtitles |
| Security | Static analysis + dependency audit | Pre-commit and CI security gates |
| Resilience | Circuit breaker state machine | Per-domain failure detection and recovery |

---

*This document describes the architectural design of Top Crawler.*
*Copyright 2026 TJ Neary. All Rights Reserved.*
