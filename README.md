# SoloEdge AIOS (Automated Infrastructure & Orchestration System)

An enterprise-grade, multi-agent orchestration framework designed for resilient digital workforce automation, asynchronous state management, and production-scale voice/text business routing. 

This repository serves as the public architectural blueprint and documentation framework for the core SoloEdge engine. Production application runtimes and client-specific data are sequestered securely within decoupled, private execution cells.

---

## 🏗️ System Architecture

The system utilizes an asynchronous, event-driven orchestration architecture. A centralized multi-agent brain handles natural language processing, semantic intent routing, and state transition logic, while a resilient n8n automation layer manages external integrations, API boundaries, and execution loops.

```mermaid
graph TD
    %% Define Styles
    classDef gateway fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff;
    classDef brain fill:#1e1b4b,stroke:#6366f1,stroke-width:2px,color:#fff;
    classDef execution fill:#064e3b,stroke:#10b981,stroke-width:2px,color:#fff;
    classDef storage fill:#7c2d12,stroke:#f97316,stroke-width:2px,color:#fff;

    %% Inbound Channels
    A[Inbound Voice SIP / Twilio] --> B[API Gateway / Webhook Router]
    C[SMS / Web Chat / Multilingual Inputs] --> B
    
    %% Core Orchestration
    B --> D[State & Context Evaluator]
    D --> E[Multi-Agent Brain / Core Logic]
    
    %% Multi-Agent Cluster
    subgraph Multi-Agent Core
        E --> E1[Riley: Multilingual Receptionist Agent]
        E --> E2[Bilingual Bridge Transition Logic]
        E --> E3[Task-Specific Execution Agents]
    end
    
    %% Execution Layer
    E1 & E2 & E3 --> F[n8n Orchestration Layer]
    F --> G[State-Change-Only Transaction Log]
    
    %% Downstream & Storage
    F --> H[External APIs / Client CRMs]
    G --> I[(Secure Local Cache / State Store)]

    %% Apply Styles
    class B,D gateway;
    class E,E1,E2,E3 brain;
    class F,H execution;
    class G,I storage;
