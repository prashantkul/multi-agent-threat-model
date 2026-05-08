# Multi-Agent Threat Model

When multiple AI agents collaborate, the threat surface expands beyond what any single-agent model captures. New categories of risk emerge from agent-to-agent trust, shared state, delegation chains, and communication channels.

This section extends the [single-agent layered model](/) into the multi-agent domain.

---

## Why Multi-Agent Systems Are Different

A single agent has a clear trust hierarchy: user instructs agent, agent uses tools, tools return results. In a multi-agent system, that hierarchy fractures.

| Property | Single Agent | Multi-Agent |
|----------|-------------|-------------|
| Trust model | Linear (user → agent → tools) | Graph (agents trust each other) |
| Authority | Single principal | Multiple principals, delegation |
| State | Private context window | Shared memory, message buses |
| Failure mode | Isolated | Cascading across agents |
| Attack surface | Tools + user input | Tools + user input + inter-agent channels |
| Auditability | One decision trail | Distributed, multi-hop trails |
| Blast radius | One agent's scope | Entire agent network |

---

## Architecture Patterns

Multi-agent systems follow one of four primary patterns, each with a distinct threat profile. Real-world systems often combine them. See [Architecture Patterns](multi-agent-patterns.md) for full diagrams, per-pattern threat tables, and attack scenarios.

| Pattern | Trust Model | Key Risk | Best For |
|---------|------------|----------|----------|
| **Supervisor** | Centralized — all trust through supervisor | Single point of failure | Task delegation with clear authority |
| **Hierarchical** | Layered — trust attenuates down tree | Deep delegation chains | Complex workflows with sub-teams |
| **Peer Mesh** | Mutual — no central authority | No central enforcement | Collaborative reasoning, debates |
| **Pipeline** | Linear — each stage trusts the previous | Poisoned stage corrupts downstream | Sequential processing, ETL |

---

## Multi-Agent Trust Boundaries

Multi-agent systems introduce new trust boundaries that don't exist in single-agent systems.

```mermaid
flowchart TB
  subgraph EXT["External Boundary"]
    User["User / API Client"]
  end

  subgraph ORCH["Orchestration Boundary"]
    Sup["Supervisor Agent"]
  end

  subgraph INTER["Inter-Agent Trust Zone"]
    AgA["Agent A"]
    AgB["Agent B"]
    AgC["Agent C"]
    Shared["Shared Memory"]
    Bus["Message Bus"]
    AgA --- Bus
    AgB --- Bus
    AgC --- Bus
    AgA --- Shared
    AgB --- Shared
    AgC --- Shared
  end

  subgraph TOOL["Tool Boundary"]
    TA["Tools A"]
    TB["Tools B"]
    TC["Tools C"]
  end

  User --> Sup
  Sup --> AgA
  Sup --> AgB
  Sup --> AgC
  AgA --> TA
  AgB --> TB
  AgC --> TC

  style EXT fill:#fee2e2,stroke:#ef4444,stroke-width:3px
  style ORCH fill:#fff7ed,stroke:#f97316,stroke-width:3px
  style INTER fill:#f0fdf4,stroke:#22c55e,stroke-width:3px
  style TOOL fill:#eff6ff,stroke:#3b82f6,stroke-width:3px
  style User fill:#ef4444,color:#fff
  style Sup fill:#f97316,color:#fff
  style AgA fill:#22c55e,color:#fff
  style AgB fill:#22c55e,color:#fff
  style AgC fill:#22c55e,color:#fff
  style Shared fill:#fbbf24,color:#000
  style Bus fill:#fbbf24,color:#000
  style TA fill:#3b82f6,color:#fff
  style TB fill:#3b82f6,color:#fff
  style TC fill:#3b82f6,color:#fff
```

### New Trust Boundaries in Multi-Agent Systems

| Boundary | What Crosses It | Key Threats |
|----------|----------------|-------------|
| **Agent ↔ Agent** | Messages, delegated tasks, results | Spoofing, tampering, injection |
| **Agent ↔ Shared State** | Reads/writes to shared memory, DB, files | Poisoning, race conditions, leakage |
| **Agent ↔ Message Bus** | Events, pub/sub messages | Injection, eavesdropping, replay |
| **Delegator ↔ Delegate** | Authority, permissions, context | Confused deputy, privilege amplification |
| **Trust Zone ↔ Trust Zone** | Cross-zone delegations | Boundary violations, scope creep |

---

## Multi-Agent Attack Surface Map

Six new attack surfaces emerge specifically from multi-agent interaction.

```mermaid
flowchart LR
  subgraph SURF["Attack Surfaces"]
    direction TB
    S1["Agent Impersonation"]
    S2["Delegation Exploit"]
    S3["Shared State Poison"]
    S4["Message Injection"]
    S5["Privilege Amplification"]
    S6["Cascade Failure"]
  end

  subgraph TARG["Targets"]
    direction TB
    T1["Agent Identity"]
    T2["Trust Chain"]
    T3["Shared Memory"]
    T4["Message Bus"]
    T5["Permission Scope"]
    T6["System Availability"]
  end

  subgraph IMP["Impact"]
    direction TB
    I1["Unauthorized Access"]
    I2["Confused Deputy"]
    I3["Data Corruption"]
    I4["Goal Hijacking"]
    I5["Full Compromise"]
    I6["Denial of Service"]
  end

  S1 --> T1 --> I1
  S2 --> T2 --> I2
  S3 --> T3 --> I3
  S4 --> T4 --> I4
  S5 --> T5 --> I5
  S6 --> T6 --> I6

  style SURF fill:#fef2f2,stroke:#ef4444,stroke-width:2px
  style TARG fill:#fff7ed,stroke:#f97316,stroke-width:2px
  style IMP fill:#fef2f2,stroke:#dc2626,stroke-width:2px
  style S1 fill:#ef4444,color:#fff
  style S2 fill:#ef4444,color:#fff
  style S3 fill:#ef4444,color:#fff
  style S4 fill:#ef4444,color:#fff
  style S5 fill:#ef4444,color:#fff
  style S6 fill:#ef4444,color:#fff
  style T1 fill:#f97316,color:#fff
  style T2 fill:#f97316,color:#fff
  style T3 fill:#f97316,color:#fff
  style T4 fill:#f97316,color:#fff
  style T5 fill:#f97316,color:#fff
  style T6 fill:#f97316,color:#fff
  style I1 fill:#dc2626,color:#fff
  style I2 fill:#dc2626,color:#fff
  style I3 fill:#dc2626,color:#fff
  style I4 fill:#dc2626,color:#fff
  style I5 fill:#dc2626,color:#fff
  style I6 fill:#dc2626,color:#fff
```

---

## Consolidated Threat Catalog

Multi-agent threats span three domains. Each is covered in depth in its own page.

### Communication Threats

| ID | Threat | Severity | Detail Page |
|----|--------|----------|-------------|
| TMA-C1 | Agent impersonation / spoofing | Critical | [Communication](multi-agent-communication.md) |
| TMA-C2 | Message tampering in transit | Critical | [Communication](multi-agent-communication.md) |
| TMA-C3 | Message replay attacks | High | [Communication](multi-agent-communication.md) |
| TMA-C4 | Eavesdropping between agents | High | [Communication](multi-agent-communication.md) |
| TMA-C5 | Message bus injection | Critical | [Communication](multi-agent-communication.md) |
| TMA-C6 | Channel flooding / DoS | High | [Communication](multi-agent-communication.md) |
| TMA-C7 | Deserialization attacks | Critical | [Communication](multi-agent-communication.md) |
| TMA-C8 | Context leakage across boundaries | High | [Communication](multi-agent-communication.md) |
| TMA-C9 | Protocol downgrade attacks | High | [Communication](multi-agent-communication.md) |
| TMA-C10 | Man-in-the-middle between agents | Critical | [Communication](multi-agent-communication.md) |

### Delegation Threats

| ID | Threat | Severity | Detail Page |
|----|--------|----------|-------------|
| TMA-D1 | Confused deputy attack | Critical | [Delegation](multi-agent-delegation.md) |
| TMA-D2 | Privilege amplification | Critical | [Delegation](multi-agent-delegation.md) |
| TMA-D3 | Authority laundering | Critical | [Delegation](multi-agent-delegation.md) |
| TMA-D4 | Delegation chain opacity | High | [Delegation](multi-agent-delegation.md) |
| TMA-D5 | Recursive delegation loop | High | [Delegation](multi-agent-delegation.md) |
| TMA-D6 | Result tampering in responses | High | [Delegation](multi-agent-delegation.md) |
| TMA-D7 | Scope creep in delegation | Medium | [Delegation](multi-agent-delegation.md) |
| TMA-D8 | Revocation failure | High | [Delegation](multi-agent-delegation.md) |
| TMA-D9 | Cross-boundary delegation | Critical | [Delegation](multi-agent-delegation.md) |
| TMA-D10 | Phantom delegation | High | [Delegation](multi-agent-delegation.md) |

### Shared State Threats

| ID | Threat | Severity | Detail Page |
|----|--------|----------|-------------|
| TMA-S1 | Shared memory poisoning | Critical | [Shared State](multi-agent-shared-state.md) |
| TMA-S2 | TOCTOU race conditions | High | [Shared State](multi-agent-shared-state.md) |
| TMA-S3 | Cross-agent data leakage | High | [Shared State](multi-agent-shared-state.md) |
| TMA-S4 | State corruption cascade | Critical | [Shared State](multi-agent-shared-state.md) |
| TMA-S5 | Unauthorized state modification | High | [Shared State](multi-agent-shared-state.md) |
| TMA-S6 | Vector database poisoning | Critical | [Shared State](multi-agent-shared-state.md) |
| TMA-S7 | Configuration tampering | Critical | [Shared State](multi-agent-shared-state.md) |
| TMA-S8 | Stale state exploitation | Medium | [Shared State](multi-agent-shared-state.md) |
| TMA-S9 | State exfiltration | High | [Shared State](multi-agent-shared-state.md) |
| TMA-S10 | Rollback attacks | High | [Shared State](multi-agent-shared-state.md) |

---

## Multi-Agent Control Framework

Controls must address the unique properties of multi-agent interaction. These build on top of the [single-agent controls](/).

```mermaid
flowchart LR
  subgraph IDENTITY["Identity Layer"]
    ID1["Agent Identity"]
    ID2["Mutual Auth"]
    ID3["Credential Rotation"]
  end

  subgraph AUTHZ["Authorization Layer"]
    AZ1["Capability Tokens"]
    AZ2["Scope Attenuation"]
    AZ3["Delegation Limits"]
  end

  subgraph COMMS["Communication Layer"]
    CM1["Message Signing"]
    CM2["Encryption"]
    CM3["Schema Validation"]
  end

  subgraph STATE["State Layer"]
    ST1["Access Control"]
    ST2["Write Validation"]
    ST3["Versioning"]
  end

  subgraph AUDIT["Audit Layer"]
    AU1["Distributed Tracing"]
    AU2["Decision Logging"]
    AU3["Anomaly Detection"]
  end

  IDENTITY --> AUTHZ
  AUTHZ --> COMMS
  COMMS --> STATE
  STATE --> AUDIT

  style IDENTITY fill:#f5f3ff,stroke:#8b5cf6,stroke-width:2px
  style AUTHZ fill:#fffbeb,stroke:#d97706,stroke-width:2px
  style COMMS fill:#f0fdfa,stroke:#0d9488,stroke-width:2px
  style STATE fill:#fdf2f8,stroke:#ec4899,stroke-width:2px
  style AUDIT fill:#eff6ff,stroke:#3b82f6,stroke-width:2px
  style ID1 fill:#8b5cf6,color:#fff
  style ID2 fill:#8b5cf6,color:#fff
  style ID3 fill:#8b5cf6,color:#fff
  style AZ1 fill:#d97706,color:#fff
  style AZ2 fill:#d97706,color:#fff
  style AZ3 fill:#d97706,color:#fff
  style CM1 fill:#0d9488,color:#fff
  style CM2 fill:#0d9488,color:#fff
  style CM3 fill:#0d9488,color:#fff
  style ST1 fill:#ec4899,color:#fff
  style ST2 fill:#ec4899,color:#fff
  style ST3 fill:#ec4899,color:#fff
  style AU1 fill:#3b82f6,color:#fff
  style AU2 fill:#3b82f6,color:#fff
  style AU3 fill:#3b82f6,color:#fff
```

### Controls Summary

| Domain | Control | Purpose |
|--------|---------|---------|
| **Identity** | Unique agent identities | Every agent has a cryptographic identity — no anonymous agents |
| **Identity** | Mutual authentication | Agents verify each other before communication |
| **Identity** | Credential rotation | Agent credentials expire and rotate automatically |
| **Authorization** | Capability tokens | Delegation carries scoped, time-limited capability tokens |
| **Authorization** | Scope attenuation | Each delegation hop reduces permissions, never increases |
| **Authorization** | Delegation depth limits | Hard cap on how many hops a delegation chain can traverse |
| **Communication** | Message signing | All inter-agent messages are cryptographically signed |
| **Communication** | Encryption | Messages encrypted in transit and at rest |
| **Communication** | Schema validation | Message payloads validated against strict schemas |
| **State** | Per-agent access control | Each agent has explicit read/write scopes on shared state |
| **State** | Write validation | Writes to shared state validated for integrity and authorization |
| **State** | Versioning | All shared state changes versioned with rollback capability |
| **Audit** | Distributed tracing | Every multi-agent interaction traced end-to-end |
| **Audit** | Decision logging | Why each routing/delegation decision was made |
| **Audit** | Anomaly detection | Behavioral baselines with alerts on deviation |

---

## Cascade Failure Model

In multi-agent systems, a compromise in one agent can cascade through the network. The blast radius depends on the architecture pattern.

```mermaid
flowchart LR
  subgraph ORIGIN["Compromise Origin"]
    Comp["Compromised Agent"]
  end

  subgraph SPREAD["Propagation"]
    SP1["Poisoned Messages"]
    SP2["Corrupted State"]
    SP3["Abused Delegation"]
  end

  subgraph IMPACT["Cascade Impact"]
    IM1["Adjacent Agents"]
    IM2["Shared Resources"]
    IM3["Downstream Agents"]
  end

  subgraph RESULT["System Impact"]
    R1["Data Corruption"]
    R2["Trust Collapse"]
    R3["Full System Failure"]
  end

  Comp --> SP1 --> IM1 --> R1
  Comp --> SP2 --> IM2 --> R2
  Comp --> SP3 --> IM3 --> R3

  style ORIGIN fill:#fef2f2,stroke:#dc2626,stroke-width:3px
  style SPREAD fill:#fff7ed,stroke:#f97316,stroke-width:2px
  style IMPACT fill:#fffbeb,stroke:#d97706,stroke-width:2px
  style RESULT fill:#fef2f2,stroke:#ef4444,stroke-width:2px
  style Comp fill:#dc2626,color:#fff
  style SP1 fill:#f97316,color:#fff
  style SP2 fill:#f97316,color:#fff
  style SP3 fill:#f97316,color:#fff
  style IM1 fill:#d97706,color:#fff
  style IM2 fill:#d97706,color:#fff
  style IM3 fill:#d97706,color:#fff
  style R1 fill:#ef4444,color:#fff
  style R2 fill:#ef4444,color:#fff
  style R3 fill:#ef4444,color:#fff
```

See the [pattern comparison matrix](multi-agent-patterns.md) for a full blast radius analysis by architecture type.

---

## Deep Dive Pages

<div class="layer-nav">
  <a href="#/multi-agent-communication" style="background:#0d9488">Communication Threats</a>
  <a href="#/multi-agent-delegation" style="background:#d97706">Delegation Chains</a>
  <a href="#/multi-agent-shared-state" style="background:#ec4899">Shared State</a>
  <a href="#/multi-agent-patterns" style="background:#6366f1">Architecture Patterns</a>
</div>
