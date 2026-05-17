# Roy Chumba
**Systems Architect | Distributed Cryptographic Protocols**

<p align="left">
  <a href="https://git.io/typing-svg">
    <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=18&pause=1000&color=00FF00&background=00000000&width=500&lines=CONNEX+Node+Status%3A+ONLINE;Enforcing+append-only+SQL+immutability...;Executing+2-of-3+Ed25519+Quorum...;Verifying+transaction+integrity...;Zero+cloud+dependencies.+Pure+math." alt="Typing SVG" />
  </a>
</p>

---

## Live System Diagnostics

<table width="100%" border="0" cellpadding="0" cellspacing="0">
  <tr>
    <td width="50%" align="center">
      <img src="https://github-readme-stats.vercel.app/api?username=Royfinnest254&show_icons=true&theme=dark&bg_color=000000&text_color=ffffff&icon_color=00ff00&border_color=333333" alt="GitHub Stats" />
    </td>
    <td width="50%" align="center">
      <img src="https://github-readme-streak-stats.herokuapp.com/?user=Royfinnest254&theme=dark&background=000000&text=ffffff&ring=00ff00&fire=00ff00&border=333333" alt="Contribution Streak" />
    </td>
  </tr>
</table>

---

## CONNEX Protocol Topology

A process-isolated payment coordination layer translating legacy ISO 8583 message streams into validated, signed, and immutable ISO 20022 XML proof bundles.

```mermaid
graph TD
    A[ISO 8583 Legacy Stream] --> B(Go Gateway)
    B --> C{Enrichment Engine}
    C -->|SHA-256 Chaining| D(Coordination Hash)
    D --> E{2-of-3 Quorum}
    E -->|Alpha / 8091| W1[Ed25519 Sign]
    E -->|Beta / 8092| W2[Ed25519 Sign]
    E -->|Gamma / 8093| W3[Ed25519 Sign]
    E -->|Quorum Met| F[(Append-Only DB)]
    F --> G[Verifiable Proof Bundle]
    style B fill:#000,stroke:#fff,stroke-width:2px,color:#fff
    style F fill:#000,stroke:#00ff00,stroke-width:2px,color:#fff
    style G fill:#000,stroke:#00ff00,stroke-width:3px,color:#fff
```

---

## Transaction & Offline Audit Sequence

```mermaid
sequenceDiagram
    autonumber
    participant Bank as Core Bank
    participant GW as Go Gateway
    participant WT as Witnesses (Alpha/Beta/Gamma)
    participant DB as SQLite Ledger
    participant Aud as Standalone Auditor (Python)

    Bank->>GW: POST ISO 8583
    GW->>GW: Parse Bitmaps & Enrich to ISO 20022
    GW->>GW: Compute Double SHA-256
    par Parallel Quorum Call
        GW->>WT: Request Signature (Hash)
        WT-->>GW: Return Ed25519 Signatures
    end
    GW->>DB: Append to Ledger (DB Triggers Block Updates/Deletes)
    GW-->>Bank: Return Signed Proof Bundle
    
    Note over Aud: 0-Trust Offline Loop
    Aud->>Aud: Recompute Hash & Verify signatures (pynacl)
    Aud-->>Aud: Return 0 (PASS) / 1 (FAIL)
```

---

## Diagnostic Specifications

```text
+-----------------------+----------------------------------------------------+
| Metric                | Implementation Standard                            |
+-----------------------+----------------------------------------------------+
| Engine Runtime        | Go 1.22 (Parallel Concurrency)                     |
| Throughput            | ~350 Transactions Per Second (TPS)                 |
| Latency               | 28ms P99 (Zero Cloud Jitter)                       |
| Verification          | Stateless Ed25519 Multi-Signatures                 |
| Ledger Immutability   | SQLite Database Triggers (Strict UPDATE/DELETE ban)|
| Footprint             | < 15MB Static Binaries                             |
+-----------------------+----------------------------------------------------+
```

---
*Zero cloud dependencies. Zero trusted third parties. Hardened cryptographic proof.*
