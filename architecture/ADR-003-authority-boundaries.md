# ADR 003: Core Model Isolation and Strict Authority Boundaries

## Status
Accepted

## Context
Large Language Models (LLMs) and multi-agent routing clusters (such as our multilingual receptionist agent, Riley) handle highly fluid, non-deterministic language processing tasks. If these agents are granted direct data-mutation access, raw database query-execution rights, or unfiltered internet-egress paths, the system becomes exposed to immense security risk—such as prompt injection exploits or accidental destructive loops.

We evaluated two structural security designs to protect multi-tenant infrastructure:
1. **Direct Agent Execution:** Giving agents direct function-calling tokens or tool wrappers to read/write directly to databases or run system-level commands.
2. **Decoupled Semantic Payloads (Isolated Boundaries):** Restricting models to exclusively output structured intent data schemas (JSON) which are captured, scrubbed, and strictly executed by isolated, deterministic software runtimes.

## Decision
We implemented a strict **Decoupled Semantic Payload Architecture**. No AI agent, voice agent, or automated orchestration node within the SoloEdge AIOS core has the structural authority to execute arbitrary system code or modify database records directly. 

## Consequences
* **Immunity to Code Injection:** If a user attempts to hijack the multilingual Riley agent via prompt injection, the model can only emit a modified text block or a structured JSON payload. Because the downstream n8n execution layer validates these schemas against strict type-guards, rogue instruction blocks are instantly dropped as invalid payloads.
* **Deterministic Sandbox Control:** All database read/write actions, webhook requests, and customer CRM operations are handled inside predefined, sandboxed deterministic blocks that check explicit authorization headers before running.
* **Audit Tracking:** Every intent expressed by an agent is saved to our structured transaction logs before it runs, making it incredibly easy to track precisely why a specific workflow executed.
* **Trade-off:** This isolation requires additional developer overhead to build and maintain explicit JSON schema validators for every unique action an agent might take. We accept this trade-off as mandatory for maintaining production enterprise-grade security.
