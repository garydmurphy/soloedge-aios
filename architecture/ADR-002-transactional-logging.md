# ADR 002: State-Change-Only Transactional Logging Architecture

## Status
Accepted

## Context
The SoloEdge framework coordinates high-frequency event cycles, including inbound webhooks, multi-agent evaluation hops, and API execution loops. Standard application architectures often rely on persistent database polling or massive monolithic log tables to track system telemetry. This introduces major performance trade-offs:
1. High database connection pool overhead on our local local hardware (NUC Command Center).
2. Unnecessary storage amplification containing redundant state frames.
3. Millisecond delays during mid-stream agent contextual state audits.

## Decision
We implemented a strict **State-Change-Only Transactional Logging** model. System telemetry and operational tracking records are emitted only when an explicit state delta occurs (e.g., an agent shifts from `Evaluating` to `Executing`, or a multi-lingual payload changes context via the Bilingual Bridge logic).

## Consequences
* **Sub-Millisecond Read Latency:** System tracing tools and auditing processes read streamlined historical logs containing zero redundant telemetry data points, optimizing read-heavy lookups.
* **Storage Optimization:** Local hardware drive footprints are significantly reduced, keeping our edge deployment light and high-throughput.
* **Deterministic Replayability:** Because logs represent explicit delta transitions, we can programmatically replay event chains exactly as they occurred to debug edge-case agent behavior without wading through idle heartbeat data.
* **Trade-off:** Debugging tools cannot rely on passive timestamps to know if a system is idle or dead. We handle this trade-off by decoupling active system health checks into a lightweight, standalone UDP heartbeat monitor.
