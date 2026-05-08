# Multi-Agent Threat Model

A layered threat model for AI agent systems — from foundation models to multi-agent orchestration.

<div class="layer-nav">
  <a href="#/layer-0-foundation-model" class="l0">Layer 0: Foundation Model</a>
  <a href="#/layer-1-agent-runtime" class="l1">Layer 1: Agent Runtime</a>
  <a href="#/layer-2-tool-integration" class="l2">Layer 2: Tool Integration</a>
  <a href="#/layer-3-orchestration" class="l3">Layer 3: Orchestration</a>
  <a href="#/layer-4-external-interface" class="l4">Layer 4: External Interface</a>
</div>

---

## The Layered Model

An AI agent is not a monolith. It is a composition of five distinct layers, each with its own trust assumptions, inputs, and attack surface.

```mermaid
block-beta
  columns 1

  block:L4:1
    columns 4
    space A1["User Interface"] A2["External APIs"] space
  end

  block:L3:1
    columns 4
    space B1["Orchestrator"] B2["Router / Planner"] space
  end

  block:L2:1
    columns 5
    C1["MCP Server"] C2["Native Tools"] C3["API Gateway"] C4["Code Executor"] C5["File I/O"]
  end

  block:L1:1
    columns 4
    space D1["System Prompt"] D2["Memory / Context"] space
  end

  block:L0:1
    columns 3
    space E1["Foundation Model"] space
  end

  L4 --> L3
  L3 --> L2
  L2 --> L1
  L1 --> L0

  style L4 fill:#ef4444,color:#fff
  style L3 fill:#f97316,color:#fff
  style L2 fill:#22c55e,color:#fff
  style L1 fill:#3b82f6,color:#fff
  style L0 fill:#8b5cf6,color:#fff
  style A1 fill:#fca5a5,color:#000
  style A2 fill:#fca5a5,color:#000
  style B1 fill:#fdba74,color:#000
  style B2 fill:#fdba74,color:#000
  style C1 fill:#86efac,color:#000
  style C2 fill:#86efac,color:#000
  style C3 fill:#86efac,color:#000
  style C4 fill:#86efac,color:#000
  style C5 fill:#86efac,color:#000
  style D1 fill:#93c5fd,color:#000
  style D2 fill:#93c5fd,color:#000
  style E1 fill:#c4b5fd,color:#000
```

| Layer | Name | Responsibility | Trust Level |
|-------|------|----------------|-------------|
| **L4** | External Interface | User inputs, API consumers, webhooks | Lowest — fully untrusted |
| **L3** | Orchestration | Routing, planning, task decomposition | Medium — consequential decisions |
| **L2** | Tool Integration | MCP servers, function calls, code exec, RAG | Medium — real-world side effects |
| **L1** | Agent Runtime | System prompt, memory, context management | High — controls what the LLM sees |
| **L0** | Foundation Model | LLM inference, token generation | Highest — opaque but assumed correct |

---

## Trust Boundaries

Each layer transition is a trust boundary. Data crosses these boundaries with different trust levels, and each crossing is a potential attack vector.

```mermaid
block-beta
  columns 1

  block:TB1["TRUST BOUNDARY: External"]:1
    columns 3
    A1["Users"] A2["Third-party APIs"] A3["Untrusted Data"]
  end

  block:TB2["TRUST BOUNDARY: Orchestration"]:1
    columns 3
    B1["Agent Routing"] B2["Task Decomposition"] B3["Inter-agent Comms"]
  end

  block:TB3["TRUST BOUNDARY: Tool Execution"]:1
    columns 4
    C1["MCP Servers"] C2["File System"] C3["Network Access"] C4["Code Sandbox"]
  end

  block:TB4["TRUST BOUNDARY: Agent Runtime"]:1
    columns 3
    D1["Prompt Assembly"] D2["Context Window"] D3["Memory Store"]
  end

  block:TB5["TRUST BOUNDARY: Model"]:1
    columns 3
    space E1["LLM Inference"] space
  end

  TB1 --> TB2
  TB2 --> TB3
  TB3 --> TB4
  TB4 --> TB5

  style TB1 fill:#fee2e2,stroke:#ef4444,stroke-width:3px,color:#991b1b
  style TB2 fill:#fff7ed,stroke:#f97316,stroke-width:3px,color:#9a3412
  style TB3 fill:#f0fdf4,stroke:#22c55e,stroke-width:3px,color:#166534
  style TB4 fill:#eff6ff,stroke:#3b82f6,stroke-width:3px,color:#1e40af
  style TB5 fill:#f5f3ff,stroke:#8b5cf6,stroke-width:3px,color:#5b21b6
  style A1 fill:#ef4444,color:#fff
  style A2 fill:#ef4444,color:#fff
  style A3 fill:#ef4444,color:#fff
  style B1 fill:#f97316,color:#fff
  style B2 fill:#f97316,color:#fff
  style B3 fill:#f97316,color:#fff
  style C1 fill:#22c55e,color:#fff
  style C2 fill:#22c55e,color:#fff
  style C3 fill:#22c55e,color:#fff
  style C4 fill:#22c55e,color:#fff
  style D1 fill:#3b82f6,color:#fff
  style D2 fill:#3b82f6,color:#fff
  style D3 fill:#3b82f6,color:#fff
  style E1 fill:#8b5cf6,color:#fff
```

### Boundary Threat Summary

| Boundary | Crossing | Key Threats |
|----------|----------|-------------|
| **B1: External to Orchestration** | Untrusted user input enters the system | Prompt injection, social engineering, input forgery |
| **B2: Orchestration to Tools** | Agent decisions trigger real-world actions | Unauthorized tool use, privilege escalation |
| **B3: Tools to Runtime** | Tool results re-enter agent context | Poisoned output, injection via results |
| **B4: Runtime to Model** | Assembled prompt sent to LLM | Context poisoning, prompt leakage |

---

## Data Flow

How data moves through a single agent, from user input to model inference and back.

```mermaid
flowchart LR
  subgraph L4["Layer 4: External"]
    UI["User Input"]
    API["API Request"]
  end

  subgraph L3["Layer 3: Orchestration"]
    Router["Router"]
    Planner["Planner"]
  end

  subgraph L2["Layer 2: Tools"]
    MCP["MCP Server"]
    Tools["Native Tools"]
    Code["Code Exec"]
  end

  subgraph L1["Layer 1: Runtime"]
    Ctx["Context Window"]
    Mem["Memory Store"]
  end

  subgraph L0["Layer 0: Model"]
    LLM["LLM Inference"]
  end

  UI --> Router
  API --> Router
  Router --> Planner
  Planner --> MCP
  Planner --> Tools
  Planner --> Code
  MCP --> Ctx
  Tools --> Ctx
  Code --> Ctx
  Mem --> Ctx
  Ctx --> LLM
  LLM --> Router

  style L4 fill:#fee2e2,stroke:#ef4444,stroke-width:2px
  style L3 fill:#fff7ed,stroke:#f97316,stroke-width:2px
  style L2 fill:#f0fdf4,stroke:#22c55e,stroke-width:2px
  style L1 fill:#eff6ff,stroke:#3b82f6,stroke-width:2px
  style L0 fill:#f5f3ff,stroke:#8b5cf6,stroke-width:2px
  style UI fill:#ef4444,color:#fff
  style API fill:#ef4444,color:#fff
  style Router fill:#f97316,color:#fff
  style Planner fill:#f97316,color:#fff
  style MCP fill:#22c55e,color:#fff
  style Tools fill:#22c55e,color:#fff
  style Code fill:#22c55e,color:#fff
  style Ctx fill:#3b82f6,color:#fff
  style Mem fill:#3b82f6,color:#fff
  style LLM fill:#8b5cf6,color:#fff
```

---

## Tool Integration Threat Surface

The tool layer is the highest-risk single-agent surface — it bridges AI with real-world side effects. See the [Layer 2 — Tool Integration](layer-2-tool-integration.md) page for the full threat catalog, MCP lifecycle analysis, and 3 detailed attack scenarios.

---

## Controls Matrix

Each layer page contains a full controls and mitigations section. Key examples:

| Layer | Control | Description |
|-------|---------|-------------|
| L4 | Input validation | Schema validation, length limits, content filtering |
| L2 | MCP server auth | mTLS or token-based auth for all MCP connections |
| L0 | Model provenance | Verify model checksums and supply chain integrity |

See each layer's detail page for the complete controls catalog.

---

## What's Next

The multi-agent threat model is now live — covering [communication](multi-agent-communication.md), [delegation](multi-agent-delegation.md), [shared state](multi-agent-shared-state.md), and [architecture patterns](multi-agent-patterns.md).

Planned expansions:

1. **Supply Chain Threats** — Model provenance, tool/plugin supply chain, dependency integrity
2. **Operational Threat Model** — Logging, monitoring, incident response for agent systems in production
