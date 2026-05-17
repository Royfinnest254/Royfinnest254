# Roy Chumba
**Founder & Lead Architect, CONNEX**

Distributed systems engineer and protocol designer specializing in high-performance financial cryptography, transactional consensus, and process-isolated settlement systems. 

I architect and maintain the **CONNEX Protocol**—a zero-trust, local-first payment coordination engine designed to bridge the legacy last-mile data gap (ISO 8583) and global settlement standards (ISO 20022) with absolute cryptographic integrity.

---

## Technical Domain Focus

### Runtime Environments & Systems Engineering
*   **Low-Level Systems:** Pure Go (Golang) for high-performance concurrency, C++, Rust
*   **Data Serialization:** ISO 8583 (1987/1993/2003 formats), ISO 20022 (`pacs.008.001.08` XML synthesis)
*   **Storage Paradigms:** Embeddable relational ledgers (CGO-free SQLite), WAL-mode optimization, database-level append-only triggers

### Cryptographic Security & Consensus Protocols
*   **Signature Schemes:** Ed25519 elliptic-curve public-key cryptography
*   **Hash Algorithms:** Double SHA-256 for non-repudiation and transaction linking
*   **Consensus Topology:** Stateless 2-of-3 Parallel Witness Quorums (Byzantine fault-tolerant design)
*   **Zero-Trust Identity:** Multi-party signature audits and standalone verification runtimes

---

## CONNEX System Architecture Topology

The CONNEX Protocol operates on a process-isolation model to secure payment transitions. The gateway coordinates parallel blind-signing requests with isolated witness nodes without exposing core credentials.

```mermaid
graph TD
    subgraph "Ingress & Parse Layer"
        A[Legacy Bank Switch / ISO 8583] -->|Raw Bitmaps| B(CONNEX Gateway)
        B -->|Deterministic Rules| C{Enrichment Engine}
    end

    subgraph "Consensus & Coordination"
        C -->|SHA-256 Hash Chaining| D(Coordination Hash)
        D -->|Parallel HTTP Sign Request| E{2-of-3 Quorum}
        
        E -->|Witness Alpha| W1(Port 8091 / Ed25519)
        E -->|Witness Beta| W2(Port 8092 / Ed25519)
        E -->|Witness Gamma| W3(Port 8093 / Ed25519)
    end

    subgraph "Immutable Ledger"
        E -->|Quorum Met| F[Append-Only SQLite Ledger]
        F -->|Trigger Blocked| G[Signed Proof Bundle]
    end

    style B fill:#111,stroke:#fff,stroke-width:2px,color:#fff
    style F fill:#111,stroke:#00ff00,stroke-width:2px,color:#fff
    style G fill:#111,stroke:#00ff00,stroke-width:3px,color:#fff
```

---

## Transactional Quorum & Verification Flow

The diagram below details the sequence of events during a single transaction, showing how a quorum is verified and stored securely.

```mermaid
sequenceDiagram
    autonumber
    participant Bank as Banking Core
    participant GW as CONNEX Gateway (8080)
    participant WT as Witnesses (Alpha/Beta/Gamma)
    participant DB as SQLite DB (data/connex.db)
    participant Aud as Standalone Python Verifier

    Bank->>GW: POST /v1/coordinate (Base64 ISO 8583)
    GW->>GW: Parse bitmaps & enrich to ISO 20022 XML
    GW->>GW: Compute SHA-256 Coordination Hash
    par Parallel Quorum Call
        GW->>WT: POST /v1/sign (Hash)
        WT->>WT: Sign with Ed25519 Private Key
        WT-->>GW: Return 64-byte Signature
    end
    Note over GW: Confirm 2 out of 3 valid signatures
    GW->>DB: INSERT transaction & proof (Triggers enforce append-only)
    GW-->>Bank: Return Signed Proof Bundle (JSON)
    
    Note over Aud: Offline Audit Loop
    Aud->>Aud: Fetch Witness Public Keys
    Aud->>Aud: Recompute Coordination Hash
    Aud->>Aud: Verify Ed25519 Signatures (pynacl)
    Aud-->>Aud: Return 0 (PASS) / 1 (FAIL)
```

---

## CONNEX Reference Implementation Specifications

| Metric | Specification | Real-World Value |
| :--- | :--- | :--- |
| **Parsing Latency** | Direct bitmap mapping in pure Go | < 2.0 ms |
| **System Throughput** | Parallel connection pooling | ~350 Transactions Per Second (TPS) |
| **End-to-End Latency** | From POST ingest to signed JSON response | 28ms P99 |
| **Storage Guarantees** | SQLite Database Triggers | Strict `ABORT` on unauthorized DML (`UPDATE`/`DELETE`) |
| **System Footprint** | Static compiled binaries | < 15MB total |
| **Verifiability** | Standalone Python interpreter | 100% decentralized, 0-trust verification |

---

## Active Research and Objectives

*   **Financial Interoperability:** Bridging the KEPSS (Kenya Electronic Payment and Settlement System) ISO 20022 framework with existing M-Pesa (retail) and banking core networks.
*   **Stateless Cryptoprocessing:** Decoupling credential management from application routing, ensuring that coordinate gateways do not hold signature keys.
*   **Hardware Security Module Integration:** Porting the witness software to secure hardware enclaves (Intel SGX / AWS Nitro Enclaves) to achieve absolute security for private key storage.
*   **Byzantine Consensus Optimization:** Reducing signature verification latency in low-bandwidth distributed environments.
