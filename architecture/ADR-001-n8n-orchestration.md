# ADR 001: Centralized Workflow Orchestration via n8n

## Status
Accepted

## Context
The SoloEdge AIOS framework must coordinate highly asynchronous, non-deterministic interactions across large language models (LLMs), runtime multi-agent loops (e.g., the Riley receptionist engine), state caches, and external third-party communication layers (Twilio SIP, webhooks). 

We evaluated two core patterns for handling this multi-step state logic:
1. **Hardcoded Application State Machine:** Building inline routing and execution steps natively inside Node.js/TypeScript application code.
2. **Visual Nodes via an External Orchestrator:** Offloading state mapping, retry-backoff policies, and external API connectors to a self-hosted n8n infrastructure node.

## Decision
We selected **self-hosted n8n** as the core transaction and execution orchestrator, while keeping the LLM-driven "Multi-Agent Brain" decoupled strictly as an intent and semantic parser.

## Consequences
* **Decoupling Core Logic:** The AI model remains isolated from direct database execution or third-party mutation calls. It evaluates incoming data and expresses an intent payload; n8n handles the heavy lifting of carrying out that intent.
* **Rapid Edge Deployment:** Workflow adjustments, webhook modifications, and custom business conditional branches can be updated instantly on our local NUC Command Center nodes without triggering a complete application rebuild.
* **Observability:** Failed execution steps, timeout boundaries, and multi-tenant routing breakdowns can be tracked visually and re-run with production payloads during active debugging phases.
* **Architectural Trade-off:** Managing complex javascript array logic or high-throughput stream formatting within visual nodes can introduce execution overhead. We mitigate this by keeping transformation schemas unified inside custom code blocks within n8n nodes rather than stringing together hundreds of single-purpose visual elements.
