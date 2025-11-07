# Data Angel Technical Infrastructure Specification (2025–2030)

*Own your data. Control your agents. Earn your worth.*

Baseline stack: **Python (FastAPI/Django)** · **Polygon/Ethereum L2** · **C2PA** · **IPFS** · **Kubernetes** · **Kafka/Redpanda**

## 1. System Overview

Data Angel is a dual-sided platform comprising a B2C privacy/rights application, a B2B consented data marketplace, and an AI Agent Layer that negotiates and enforces data usage policies. The platform uses a hybrid on-chain/off-chain design where encrypted data and analytics remain off-chain, while immutable consent receipts, tokenized provenance, and DAO governance are anchored on-chain.

Primary domains:

- **B2C App**: Browser extension and mobile app providing visibility, control, and earnings transparency for individual users.
- **B2B Marketplace**: SDK and dashboard enabling consent validation, cohort licensing, and auditing for businesses.
- **Agent Layer**: User AI Agents and Business AI Agents coordinate access, pricing, terms, and revocation.
- **Provenance & Tokenization**: C2PA metadata, NFT minting, royalty smart contracts, and IPFS storage pointers ensure trusted provenance.

## 2. Core Architecture Layers

### a. User Layer (B2C)

- **Web**: React with Tailwind CSS.
- **Mobile**: React Native applications for iOS and Android.
- **Browser Extension**: Manifest v3 with network instrumentation for tracker detection and a local policy cache.
- **AI Policy Co-Pilot**: LLM-powered front-end with safeguard rails and on-device inference fallback where feasible.
- **Encrypted User Vaults**: Client-side encryption, per-user keys managed in KMS, with optional zero-knowledge attestations.

### b. Agent Layer

- **User AI Agent**: Interprets policies, approves or denies consent, negotiates price, and executes automated data subject requests (DSRs).
- **Business AI Agent**: Initiates data requests, validates compliance, controls spend, and integrates via webhooks.
- **Agent Protocol**: HTTP/2 and WebSocket communication, JSON-LD payloads, signed requests, rate limiting, and replay protection.
- **Reputation & Scoring**: Trust index derived from breach-free history, dispute outcomes, and user ratings.

### c. Platform Layer

- **Consent Ledger**: Hybrid ledger using PostgreSQL operational storage with on-chain anchors (Polygon) for immutability.
- **Tokenization Engine**: Pipeline from C2PA-embedded assets to IPFS pinning, NFT minting, royalty contract linkage, and watermark verification.
- **Data Vaults**: S3-compatible object stores with server-side encryption and client-side key wrapping; IPFS for content addressing.
- **Smart-Contract Middleware**: Solidity contracts manage royalties, license durations, revocations, and DAO parameters.

### d. Data & AI Layer

- **Event Streaming**: Kafka/Redpanda streams consent events, telemetry, bids, and payouts.
- **Processing**: Python services built with FastAPI, Celery/Arq workers, ClickHouse analytics, and Neo4j data-flow graphs.
- **AI Engines**: LangChain policy engine, vector databases (pgvector/Weaviate) for consent and RoPA retrieval, and PyTorch models for anomaly detection.
- **Synthetic/Privacy**: Diffusion/LLM-based anonymization, k-anonymity, and differential privacy for research exports.

### e. Integration Layer

- **SDKs**: TypeScript (web), Kotlin/Swift (mobile), Python (server), and C# (enterprise).
- **Authentication**: OAuth2/OIDC and DID/VC for verified identities with mutual TLS for server-to-server communications.
- **Webhooks**: Events for consent updates, license issuance, royalty payouts, and breach alerts.
- **External APIs**: Integrations with Have I Been Pwned, C2PA registry, data clean rooms (AWS/Snowflake), and payment providers (Stripe/SEPA/USDC).

## 3. Security Architecture

- **Encryption**: TLS 1.3 for transport, AES-256-GCM for data at rest, envelope keys stored in HSM/KMS, and client-side key wrapping for user vaults.
- **Identity & Auth**: DID with Verifiable Credentials, WebAuthn/FIDO2, scoped OAuth tokens, and short-lived JWTs with rotation.
- **Consent Proofs**: On-chain anchors (Merkle roots) for consent receipts, revocation lists, and non-repudiation via ECDSA signatures.
- **Secrets & Supply Chain**: SOPS/Sealed Secrets, SBOM tracking, Sigstore/Cosign signing, image allowlists, and runtime policy enforcement via OPA/Gatekeeper.
- **Privacy**: ZK-friendly hashes for selective disclosure, strict data minimization, and enforcement of purpose and retention policies.

## 4. Scalability & Reliability

- **Orchestration**: Kubernetes (EKS/GKE) with horizontal/vertical pod autoscaling, multi-AZ deployments, and read replicas.
- **Caching**: Redis for sessions, policy caching, and agent negotiations; CDN for static assets.
- **Partitioning**: Topic sharding by region and tenant, idempotent producers, and pursuit of exactly-once semantics.
- **Resilience**: Circuit breakers, bulkheads, exponential backoff, chaos testing, RPO ≤ 5 minutes, and RTO ≤ 30 minutes.
- **Observability**: OpenTelemetry tracing, Prometheus with Grafana dashboards, ELK stack, and SLO-driven error budgets.

## 5. Tokenization & Blockchain Framework (Polygon/Ethereum L2)

- **Contracts**: ERC-721/1155 assets, ERC-2981 royalties, and custom LicenseManager contracts for time-boxed, access-scoped rights.
- **Consent Anchors**: ConsentReceiptRegistry mapping consent hashes to issuer and subject DIDs with revocation tracking.
- **DAO**: Governance token with timelock, treasury for incentives and staking, and slashing penalties for policy breaches.
- **Gas Strategy**: Batch minting, meta-transactions, L2 bridges, and USDC on-chain payouts for EARN tier participants.

## 6. Agent Intelligence Architecture

- **Communication**: WebSocket channels for negotiation, signed JSON-LD payloads, and schema validation.
- **Reasoning**: Policy-based planners with safety rails; value function combines user price floors, risk scores, and purpose alignment.
- **Memory**: Per-user vector stores and consent knowledge bases with time-decayed histories plus dispute and appeal records.
- **Safety**: PII redaction, purpose-bound tokens, sandboxed tool usage, and defenses against adversarial prompts.

## 7. Data Pipeline & Compliance Logging

- **Ingestion**: Tracker telemetry flows into Kafka, enrichment pipelines, and ClickHouse/Neo4j for data-flow graphing.
- **Lineage**: End-to-end lineage powered by OpenLineage/Marquez, RoPA exports, and DPIA attachments.
- **Real-Time Checks**: Pre-flight policy evaluation, rate and volume caps, and dynamic purpose adjustment.
- **Reporting**: Article 30 logs, AI Act transparency packages, DSAR/auto-DSR processors, and regulator export packs.

## 8. DevOps & CI/CD

- **Pipelines**: GitHub Actions, Terraform infrastructure-as-code, Helm charts, and environment parity across dev/stage/prod.
- **Security**: DAST/SAST tooling, dependency pinning, pre-commit hooks, least privilege IAM, and quarterly penetration tests.
- **Release**: Blue/green and canary deployments, feature flags, database migrations with gh-ost/Liquibase, and <2 minute rollbacks.

## 9. Non-Functional Requirements (NFRs)

- **Performance**: <100 ms p95 consent validation latency; <500 ms p95 agent negotiation latency (with caching).
- **Availability**: 99.95% platform uptime; 99.9% on-chain anchor service uptime.
- **Compliance**: GDPR, CCPA/CPRA, POPIA, AI Act baseline, and pursuit of ISO 27001/SOC 2 within 18 months.
- **Accessibility**: WCAG 2.1 AA compliance for all user-facing applications.

## 10. Infrastructure Roadmap (2025–2030)

- **Phase 1 (0–6 months)**: MVP infrastructure featuring the browser extension, consent ledger, vaults, basic SDK, and single-region Kubernetes.
- **Phase 2 (6–18 months)**: Agent middleware, marketplace beta, multi-region expansion, ClickHouse and Neo4j adoption, and initial on-chain registry deployment.
- **Phase 3 (18–30 months)**: White-label offerings, insurer integrations, enterprise SLAs, DAO bootstrap, and global CDN with data residency controls.
- **Phase 4 (30–60 months)**: Agent-to-agent real-time bidding, secondary license markets, zero-knowledge consent proofs, and progressive decentralization.

## Appendix A — Agent-to-Agent Negotiation Sequence

```
Business AI Agent  -> Data Angel API: POST /cohorts/request {purpose, size, duration, maxPrice}
Data Angel Policy  -> Consent KB/Vector DB: Retrieve purpose + policy constraints
Data Angel Policy  -> Risk Engine: Evaluate data risk & compliance
Data Angel API     -> User AI Agent(s): NOTIFY {purpose, price, scope, duration, vendorRep}
User AI Agent      -> Local Policy: Check price floor, scope, reputation, risk tolerance
User AI Agent      -> Data Angel API: COUNTER {minPrice, scope refinement, frequency cap}
Business AI Agent  -> Data Angel API: ACCEPT {final terms}
Data Angel API     -> LicenseManager (Polygon L2): issueLicense(tokenId, vendor, scope, expiry)
Data Angel API     -> ConsentReceiptRegistry: anchor receipt hash + revocation pointer
Business AI Agent  -> Webhook: LICENSE_ISSUED {cohortToken, expiry, proof}
Data Angel Payouts -> USDC Transfer: Royalty split to users (ERC-20)
User AI Agent      -> Local Vault: Update audit timeline & earnings
```

## Appendix B — Zero-Trust Network Reference Architecture

```
[User Devices] --mTLS--> [Edge/API Gateway (WAF, Rate Limit, AuthN)] --mTLS--> [Service Mesh (mTLS, OPA)]
      |                                                                             |
      |                                    +-------------------------+              |
      +-- WebAuthn/FIDO2 -----------------> | Identity (OIDC, DID/VC) | <-----------+
                                            +-------------------------+
                                                             |
                                                             v
                                  +----------------------------------------+
                                  | Microservices (K8s, sidecar mTLS)      |
                                  |  - Policy/Consent Svc                  |
                                  |  - Agent Negotiation Svc               |
                                  |  - Tokenization/NFT Svc                |
                                  |  - Ledger Anchor Svc                   |
                                  |  - Payouts/Royalty Svc                 |
                                  +-----------------+----------------------+
                                                    |
                         +--------------------------+---------------------------+
                         v                                                          v
                [Data Stores: Postgres/ClickHouse/Neo4j]                    [Secrets: KMS/HSM]
                (row-level policy, CMEK, field encryption)                  (key wrap, rotation)
                                                    |
                                                    v
                                         [Event Bus: Kafka/Redpanda]
                                         (signed events, partitions)
                                                    |
                                                    v
                                   [Polygon L2 Anchors & Smart Contracts]
                                   (ConsentReceiptRegistry, LicenseManager, ERC-2981)
```

## Appendix C — Solidity Interface Stubs (Polygon/Ethereum L2)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

/// @title IConsentReceiptRegistry
/// @notice Anchors consent receipt hashes and supports revocation.
interface IConsentReceiptRegistry {
    /// @dev Emitted when a consent receipt is anchored.
    event ConsentAnchored(bytes32 indexed receiptHash, address indexed subjectDid, address indexed issuer, uint256 blockTime);
    /// @dev Emitted when a consent is revoked.
    event ConsentRevoked(bytes32 indexed receiptHash, address indexed subjectDid, uint256 blockTime);

    /// @notice Anchor a consent receipt.
    /// @param receiptHash keccak256 hash of the receipt payload.
    /// @param subjectDid DID of the data subject (compressed or resolver address).
    function anchorConsent(bytes32 receiptHash, address subjectDid) external;

    /// @notice Revoke a consent receipt.
    /// @param receiptHash keccak256 hash of the original receipt.
    function revokeConsent(bytes32 receiptHash) external;

    /// @notice Check if a consent is currently valid (not revoked).
    function isValid(bytes32 receiptHash) external view returns (bool);
}

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

/// @title ILicenseManager
/// @notice Issues time-bound, scope-bound licenses mapped to tokenized cohorts.
interface ILicenseManager {
    struct LicenseTerms {
        address licensee;       // Business wallet
        uint64  startTs;        // start timestamp
        uint64  endTs;          // end timestamp
        bytes32 purposeHash;    // hash of purpose/scope JSON
        uint256 pricePaid;      // total paid in wei or USDC via bridge
        bool    active;         // status
    }

    /// @dev Emitted when a license is issued.
    event LicenseIssued(uint256 indexed cohortTokenId, bytes32 indexed licenseId, address indexed licensee);
    /// @dev Emitted when a license is revoked.
    event LicenseRevoked(bytes32 indexed licenseId, address indexed revoker);

    /// @notice Issue a license for a cohort token with specified terms.
    function issueLicense(uint256 cohortTokenId, LicenseTerms calldata terms) external returns (bytes32 licenseId);

    /// @notice Revoke an active license (on breach or user-initiated revocation).
    function revokeLicense(bytes32 licenseId) external;

    /// @notice Get current license terms.
    function getLicense(bytes32 licenseId) external view returns (LicenseTerms memory);
}

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

/// @title IRoyaltyDistributor (ERC-2981 compatible)
/// @notice Splits incoming payments among cohort participants and platform fee.
interface IRoyaltyDistributor {
    /// @dev Emitted on distribution.
    event RoyaltyDistributed(bytes32 indexed distributionId, address token, uint256 amount, uint256 participants);

    /// @notice Distribute a payment amount (ETH or ERC20) to participants.
    /// @param token address(0) for ETH or ERC20 token address (e.g., USDC)
    /// @param cohortTokenId Cohort token id used to resolve the participant set and weights
    /// @param amount Total amount to distribute
    function distribute(address token, uint256 cohortTokenId, uint256 amount) external payable;
}
```
