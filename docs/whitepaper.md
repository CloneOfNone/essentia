# Essentia Network White Paper

### Version 0.6.0 — **DRAFT FOR INTERNAL REVIEW**

_(Supersedes v0.5.0; Society-of-Renewal alignment, governance norms, privacy, and civic-cost specifications consolidated as of 29 Oct 2025)_

---

## Changelog (v0.5.0 → v0.6.0)

- **Scope pivot:** Essentia defined as a **post-quantum civic ledger** aligned to the Society of Renewal (identity, attestations, governance, UBI rails), not a general-purpose L1.
- **Identity:** Introduced **Person (DID)** with passkeys + social recovery; claims as verifiable credentials with selective disclosure.
- **Value rails:** Added **Essential Unit (𝒰)** for UBI (non-transferable, expiring, indexed) alongside **Essent (ℰ)** for contribution rewards; separated issuance streams.
- **Governance (normative):** Private ballots with public proofs; **RCV**, **revocable delegation** (domain-scoped), **QV for budgeting only**; eligibility snapshots and deterministic tie-break rules.
- **Civic cost model:** **Voting is fee-less**; introduced **Civic Gas Pool** and **non-transferable Voting Credits** for anti-spam and accessibility.
- **Attestations & minting:** Evidence tiers, challenge windows, geographic normalization; reputation (STAR/FLAME) influences attestations only, never rights.
- **Security & bootstrap:** No premine; validator operational bonds; public genesis ceremony; verifier CLI requirements and public mock-election readiness path.

---

## Abstract

Essentia is a post-quantum secure **civic ledger** providing (1) **self-sovereign identity** via a native DID method, (2) **verifiable governance** with private ballots and public proofs, (3) **attestation rails** for real-world contributions, and (4) **value rails** comprising a transferable token (**Essent, ℰ**) and a non-transferable, expiring instrument for essentials (**Essential Unit, 𝒰**) used to deliver an inflation-aware **Freedom Floor (UBI)**.

Minting of ℰ follows **proof-of-contribution** attested by elected **Attestors** and weighted by a **Web-of-Trust** reputation model (**STAR/FLAME**). 𝒰 issuance is policy-driven and separate from ℰ supply. Rights (vote, UBI, due process) are never reputation-gated. Ballots are private; outcomes are independently verifiable.

---

## 1. Purpose & Motivation

1. **Civic spine:** Neutral, verifiable rails for identity, voting, attestation, and Freedom-Floor disbursement.
2. **Freedom with compassion:** Material security enables education-first safety systems.
3. **Proof over authority:** Mint ℰ for _validated_ positive contribution with publicly verifiable evidence.
4. **Dignity constraint:** Reputation tunes _influence_, not _rights_.

---

## 2. Core Concepts (Human-Readable)

| Category         | Name                   | Type                        | Role                               | Notes                                                      |
| ---------------- | ---------------------- | --------------------------- | ---------------------------------- | ---------------------------------------------------------- |
| **Identity**     | **Person (DID)**       | Object                      | Self-sovereign identity anchor     | `did:essentia:<mbase>`; passkeys for auth; social recovery |
|                  | **Credential**         | VC                          | Verifiable claim                   | BBS+ selective disclosure; PoP, roles, learning            |
| **Attestation**  | **Attestor**           | Role (VC)                   | Elected reviewer of evidence       | Influence weighted by STAR; penalties do not remove rights |
|                  | **Attestation**        | Object                      | Co-signed claim about contribution | Payload off-chain (IPFS/Arweave); hash on-chain            |
| **Contribution** | **Purpose**            | Token                       | Program with mission & budget      | Container for Quests; policy & metrics                     |
|                  | **Quest**              | Token                       | Discrete task aiding a Purpose     | Scope, complexity, evidence tier                           |
|                  | **Journey**            | Log                         | Receipt chain for Quest execution  | Merkle-linked artifacts                                    |
| **Reputation**   | **STAR**               | Scalar                      | Bounded influence weight (≥0)      | Weights attestations & surfacing                           |
|                  | **FLAME**              | [0,1]                       | Time-decaying fraud flag           | Reduces influence; exponential decay                       |
| **Value**        | **Essent (ℰ)**         | Fungible token              | Reward for valid contribution      | Transferable; bounded issuance                             |
|                  | **Essential Unit (𝒰)** | Non-transferable instrument | UBI for essentials                 | Spend-restricted; expiring; indexed                        |

**Guarantees:** Voting and UBI never depend on STAR/FLAME. No personal data is written on-chain.

---

## 3. Identity & Credentials

### 3.1 DID Method

- **Method:** `did:essentia:<multibase>`. DID Document exposes:  
  **Authentication** (WebAuthn/Passkeys — Ed25519), **Assertion/Spend** (CRYSTALS-Dilithium-3), optional **Key Agreement** (hybrid).
- **Rotation & Recovery:** Signed updates; social recovery with 3–5 guardians or hardware escrow; on-chain hash anchoring.
- **Privacy:** DID Docs contain no PII; claims exist as verifiable credentials (VCs) off-chain.

### 3.2 Verifiable Credentials

- **Proof-of-Personhood (PoP):** one-to-one person↔DID assurance via privacy-preserving ceremony.
- **Role Credentials:** Attestor, Steward, Validator.
- **Learning/Competency:** Academy badges (e.g., UPE Core, Mediator 101).
- **Selective Disclosure:** BBS+ signatures and ZK predicates reveal only necessary attributes.
- **Anchoring:** Issuance/revocation hashes recorded on-chain.

---

## 4. Governance

### 4.1 Mechanisms

- **Ballot privacy & verifiability:** Elections specify either **Commit–Reveal** (commitment $C=H(vote, salt)$, later reveal) or **MACI-style encrypted ballots**. Receipt-free mode is offered. On-chain artifacts include proposal hash, ballot commitments or encrypted payloads, and a result digest with proofs.
- **Ranked-Choice Voting (RCV):** Single-winner uses instant-runoff; multi-winner uses Droop quota. Tie-break seed = $H(\text{proposalId}\,\|\,\text{snapshotHeight})$. Exhausted ballots do not transfer further.
- **Revocable Delegation (domain-scoped):** Delegations form a directed acyclic graph per domain; cycles are resolved by removing the newest edge. A direct vote overrides delegation for that proposal.
- **Quadratic Voting (QV) — budgeting only:** Each voter receives a fixed, non-transferable credit budget $B$. Casting $k$ votes on an option costs $k^2$. Credits expire at round end and are not tradable.
- **Quorum & thresholds:** Network-wide constitutional measures require **two-of-three** supermajorities across the **Assembly of Members**, relevant **Steward Circle**, and **Validator Commons**. Operational items: Steward Circle + Assembly simple majority with quorum ≥ max(1% of eligible, 500).
- **Timing windows:** Commit open/close and reveal open/close (or MACI equivalents) are declared per election with grace periods. Eligibility snapshot height $h_s$ is stored in the proposal object.

### 4.2 Auditability

Publish canonical JSON artifacts: (i) ballot commitments/encrypted payloads, (ii) RCV round tables, (iii) delegation resolution trace, (iv) result digest and proof hashes. A **Verifier CLI** recomputes eligibility at $h_s$, replays RCV rounds, resolves delegations, validates proofs, and emits a canonical results file.

### 4.3 Civic Cost Model

- **Fee-less participation:** Submitting commitments, reveals, delegation changes, or QV allocations requires no ℰ or 𝒰 balances.
- **Civic Gas Pool (CGP):** Treasury-funded pool covers chain costs of election transactions. Budgets are approved per epoch; unspent funds roll forward.
- **Voting Credits (VCRED):** Per-election, non-transferable credits entitle each eligible voter to a bounded number of ballot operations (e.g., one commit, one optional update, one reveal). VCRED are free and expire when the election ends.
- **Rate-limits & relays:** DID-scoped API rate-limits and commitment size caps deter spam. Official relayers accept ballots over multiple transports (web, Tor, low-bandwidth endpoints) and are reimbursed from CGP; envelopes are publicly auditable.  
  VCRED cover **updates** as well as initial commits/reveals; all such transactions are reimbursed from the CGP.

### 4.4 Vote Updates (Amendments)

- **Commit–Reveal:** Each voter submits commitments with a strictly increasing sequence number `seq`. Only the **highest-seq** commitment that is subsequently **revealed** within the reveal window is counted; earlier commitments are ignored.  
  `commit = H(proposalId ∥ snapshotHeight ∥ seq ∥ encodedVote ∥ salt)`. Reveals must include the matching `(seq, encodedVote, salt)`. Sequence reuse is invalid.
- **MACI-style:** A voter may submit update messages; the **last valid** message before close supersedes prior ones. The tally proves in zero knowledge that exactly one final vote per eligible voter was applied.
- **Delegation precedence:** A direct vote for a proposal overrides any delegation for that proposal; submitting a new direct vote replaces the previous one.
- **Abuse resistance:** VCRED bound the number of ballot operations; DID-scoped rate-limits may apply.

---

## 5. Contribution, Attestation & Minting

### 5.1 Evidence Tiers

Quests select an **evidence tier (ET)** that caps impact: **ET-1** (media + location), **ET-2** (third-party QC or peer review), **ET-3** (replication/audited impact). Higher tiers permit higher impact caps; higher penalty for falsehood.

### 5.2 Attestation Flow

1. **Submission:** Executor stores evidence off-chain; posts content hash + metadata on-chain.
2. **Review:** At least _m_ Attestors verify and co-sign an attestation leaf.
3. **Aggregation:** Merkle root $R = H(\pi \parallel \sigma_1 … \sigma_m)$ recorded as **AttestationRef**.
4. **Challenge window:** Counter-evidence may reduce impact or apply FLAME to signers with restorative steps.
5. **Mint:** Valid submissions mint ℰ via §5.3.

### 5.3 Minting Function (ℰ)

Let _c_ be complexity, _i_ bounded impact (per ET), $\mathbf{s}$ the vector of Attestor STARs, and $I*{\max}$ the epoch cap:

$$
M = \min\!\left( c^{\alpha} \cdot i^{\beta} \cdot \sqrt{\sum_j s_j},\; I*{\max} \right),
\quad \alpha,\beta\in(0,1)
$$

Defaults: $\alpha=0.6,\ \beta=0.4$. Impact components are geographically normalized.

### 5.4 Reputation Dynamics

$$
\mathrm{STAR}_t(v) = \gamma \!\!\sum_{(u\to v, w, \tau=+) \in E_t}\!\! w\cdot \mathrm{STAR}*{t-1}(u)\cdot (1-\mathrm{FLAME}_{t-1}(u)),
\quad \gamma\in(0,1)
$$

$$
\mathrm{FLAME}\_t(v)=\mathrm{FLAME}_{t-1}(v)\, e^{-\lambda \Delta t}
$$

(half-life ≈ 90 days). Reputation influences attestations and surfacing only.

---

## 6. Value & Monetary Policy

### 6.1 Instruments

- **Essent (ℰ):** Transferable, fungible; for exchange, grants, staking bonds, and fees.
- **Essential Unit (𝒰):** Non-transferable, spend-restricted to essentials, expiring; the UBI rail.

### 6.2 Supply Streams

- **Contribution mint (ℰ):** Bounded by per-epoch cap $I\_{\max}$.
- **UBI issuance (𝒰):** Separate contract; not counted against ℰ cap.
- **Grants:** Treasury disbursements in ℰ or 𝒰.

### 6.3 Inflation & Indexing

$$
\Delta S*{\mathcal{E}} \le S*{\text{prev}}\cdot \rho,\quad \rho \in [2\%,5\%]\ \text{annualized}
$$

𝒰 auto-indexes quarterly to an **Essentials CPI** via a medianized oracle; bounded governance veto/raise. 𝒰 expiration dampens hoarding.

### 6.4 Treasury & Reserves

Multi-sig treasury with on-chain policy; quarterly disclosures. Optional buffers for smoothing; triggers are published.

---

## 7. Ledger & Consensus

### 7.1 Data Model

On-chain: DID anchors, VC issuance/revocation hashes, AttestationRefs, proposal refs, ballot commitments, 𝒰 epoch parameters, ℰ UTXOs.  
Off-chain: evidence bundles (content-addressed); replicated gateways with integrity checks.

### 7.2 Consensus

Narwhal/Bullshark-style DAG mempool and **HotStuff BFT** finality. Validators are public DIDs posting **operational bonds** (slashed for downtime/double-sign). No premine.

### 7.3 Keys & Signatures

Authentication: **Ed25519** (passkeys). Spend/blocks: **CRYSTALS-Dilithium-3**. VC selective disclosure: **BBS+**; ZK predicates for attributes. Hashing: Poseidon/Blake3 (domain-separated). Ballot privacy: Commit–Reveal or MACI-style with coordinator proofs.

---

## 8. Voting Privacy & Verification

Ballots are unlinkable to identities; receipt-free mode is available. Published artifacts include encrypted/committed ballots, RCV round tables, delegation traces, result digests, and proof hashes. The **Essentia Verifier CLI** reconstructs eligibility at snapshot, resolves delegation, replays RCV rounds, validates proofs, and emits canonical results.

---

## 9. Security Model & Threats

| Threat                  | Mitigation                                                                     |
| ----------------------- | ------------------------------------------------------------------------------ |
| Sybil farms             | PoP ceremonies; limited initial trust budgets; DID rate-limits; edge caps      |
| Attestor collusion      | FLAME spillover; restorative steps; auditor sampling; challenge windows        |
| CPI oracle manipulation | Multi-source medianization; circuit-breakers; governance veto                  |
| Ballot deanonymization  | Mixnets/MACI; timing padding; receipt-freeness; coercion reporting             |
| Validator censorship    | DAG mempool; slashing; diverse hosting; home-hostable nodes; multi-uplink mesh |
| Key compromise          | Social recovery; multi-device passkeys; rapid rotation; wallet warnings        |
| Off-chain data loss     | Redundant pinning; integrity checks via on-chain hashes                        |

---

## 10. Bootstrap & Genesis

Founding set: 7–11 validators across regions; conflicts disclosed; resign-by-design timelines. No premine. Attestors elected by public vote with revocable role credentials. Public genesis ceremony with split entropy; transcripts and hashes published. Alpha faucet with transparent limits. First governance smoke test runs RCV with full “verify it yourself” materials.

---

## 11. Client Surfaces & Developer Experience

Wallet (web/mobile) for DID/passkeys, ℰ/𝒰, voting, proofs, and recovery. CLI for DID/VC lifecycle, proposals, vote verification, UBI epochs. SDKs in TypeScript and Rust (DID, VC, ledger RPC, proofs). Normative docs and runnable examples with open test vectors.

---

## 12. Roadmap & Milestones

| Phase          | Milestone                                                                   | Target      |
| -------------- | --------------------------------------------------------------------------- | ----------- |
| **α Testnet**  | DID method, VC anchoring, ℰ UTXO, AttestationRefs, verifier CLI (mock vote) | **Q4 2025** |
| **β Testnet**  | 𝒰 rails + Essentials CPI oracle, commit–reveal ballots, wallet beta         | **Q2 2026** |
| **Mainnet v1** | Delegation, QV budgeting, verifiable UBI indexing, academy-issued VCs       | **Q4 2026** |

---

## 13. Symbols & Parameters

| Symbol                                                        | Domain | Meaning                                                                  |
| ------------------------------------------------------------- | ------ | ------------------------------------------------------------------------ |
| $\mathcal{E}$                                               | —      | Essent token (transferable)                                              |
| $\mathcal{U}$                                               | —      | Essential Unit (UBI instrument; non-transferable, expiring)              |
| $c \in \mathbb{R}\_{\ge 0}$                                 | —      | Quest complexity parameter                                               |
| $i \in \mathbb{R}\_{\ge 0}$                                 | —      | Bounded impact score (by evidence tier and normalization)                |
| $\alpha, \beta \in (0,1) $                                  | —      | Minting exponents for complexity/impact                                  |
| $\mathbf{s} = (s_j){j=1..m},\ s_j \in \mathbb{R}{\ge 0}$    | —      | Attestor STAR weights participating in a mint                            |
| $I*{\max} \in \mathbb{R}*{\ge 0}$                           | —      | Per-epoch cap on ℰ minted via contribution                               |
| $S*{\text{prev}} \in \mathbb{R}*{\ge 0}$                    | —      | Previous total ℰ supply                                                  |
| $\rho \in [0.02, 0.05]$                                     | —      | Annualized upper bound on ℰ supply growth                                |
| $\mathrm{STAR}t(v) \in \mathbb{R}{\ge 0}$                   | —      | Influence weight of node $v$ at time $t$                                 |
| $\gamma \in (0,1)$                                          | —      | Diffusion damping factor in STAR computation                             |
| $\mathrm{FLAME}\_t(v) \in [0,1]$                            | —      | Decaying fraud flag of node $v$ at time $t$                              |
| $\lambda > 0$                                               | —      | FLAME exponential decay rate (half-life ≈ 90 days)                       |
| $G=(V,E,W,T)$                                               | —      | Trust multigraph; $W:E\to[-1,1]$ weights; $T$ edge types                 |
| $A$                                                         | —      | Weighted adjacency matrix derived from $G$                               |
| $b$                                                         | —      | Base trust vector                                                        |
| $H(\cdot)$                                                  | —      | Domain-separated cryptographic hash (e.g., Blake3/Poseidon)              |
| $h_s \in \mathbb{N}$                                        | —      | Eligibility snapshot block height for an election                        |
| `seq` $\in \mathbb{N}$                                      | —      | Monotone sequence number for vote updates (commit–reveal)                |
| `proposalId`                                                | —      | Canonical proposal reference (hash)                                      |
| `encodedVote`                                               | —      | Canonical encoding of ballot contents (ranking, selection, or QV vector) |
| `salt`                                                      | —      | Per-commitment nonce for hiding vote contents                            |
| $B \in \mathbb{N}$                                          | —      | QV credit budget per voter                                               |
| $k \in \mathbb{N}$                                          | —      | QV intensity on a single option; cost $k^2$ credits                      |

**Minting function.**  
$M = \min!\left( c^{\alpha} \cdot i^{\beta} \cdot \sqrt{\sum_{j} s_{j}},, I_{\max} \right)$.

**Supply cap.**  
$\Delta S*{\mathcal{E}} \le S*{\text{prev}} \cdot \rho $.

**Reputation dynamics.**  
$\mathrm{STAR}t(v) = \gamma \sum{(u\to v,, w,, \tau=+) \in E_t} w \cdot \mathrm{STAR}{t-1}(u) \cdot \bigl(1-\mathrm{FLAME}{t-1}(u)\bigr)$.  
$\mathrm{FLAME}\_t(v)=\mathrm{FLAME}\_{t-1}(v)\, e^{-\lambda \Delta t}$.

---

## 14. Formal Notation (Extract)

Graph $G=(V,E,W,T)$ with $W:E\to[-1,1]$, $T$ edge types.  
STAR diffusion $\mathrm{STAR}=(I-\gamma A)^{-1} b $, weighted adjacency $A$, base vector $b$.  
FLAME decay $\mathrm{FLAME}(t)=\mathrm{FLAME}(t_0), e^{-\lambda (t-t_0)}$.  
ℰ mint cap $\Delta S_{\mathcal{E}} \le S_{\text{prev}},\rho$.  
RCV uses deterministic tie-break seed $H(\text{proposalId}\,\|\,\text{snapshotHeight})$.

---

## 15. Charter Compliance (Traceability)

Rights not reputation-gated; ballots private with public proofs; education-issued VCs unlock stewardship utility (not rights); transparent budgets and “What Changed” logs; privacy by default with selective disclosure.

---

## 16. Glossary

**Essent (ℰ):** Transferable token minted for validated contribution.  
**Essential Unit (𝒰):** Non-transferable, expiring instrument for essentials (UBI).  
**DID:** Decentralized Identifier under `did:essentia`.  
**VC:** Verifiable Credential (JSON-LD).  
**Attestor:** Elected evidence reviewer.  
**STAR/FLAME:** Influence weight / decaying fraud flag (never gates rights).  
**RCV / QV:** Ranked-Choice Voting / Quadratic Voting.  
**MACI:** Minimal Anti-Collusion Infrastructure.  
**CGP:** Civic Gas Pool.  
**VCRED:** Voting Credits.

---

### Appendix A — Mathematical Details (Outline)

- Parameter tables for $\alpha,\beta,\gamma,\lambda,\rho$.
- ET-tiered impact caps and normalization functions.
- RCV proof transcript format; delegation resolution algorithm.
- ZK statement definitions for ballot correctness and tally soundness.

### Appendix B — API Schemas & Canonical Artifacts (Excerpt)

**Ballot commitment (commit phase)**

```json
{
  "type": "rcvCommit",
  "proposalId": "0xabc...",
  "snapshotHeight": 123456,
  "voter": "did:essentia:...",
  "commit": "0xH(commitPayload)",
  "seq": 2,
  "nonce": "0x...",
  "sig": "0x..."
}
```

**RCV round table (tally artifact)**

```json
{
  "proposalId": "0xabc...",
  "snapshotHeight": 123456,
  "rounds": [
    {
      "eliminated": ["OptionC"],
      "transfers": { "OptionA": 124, "OptionB": 201 },
      "exhausted": 9,
      "proof": { "hash": "0x...", "zk": "0x..." }
    }
  ],
  "resultDigest": "0x..."
}
```

**Delegation resolution trace (domain-scoped)**

```json
{
  "domain": "civic.health",
  "snapshotHeight": 123456,
  "paths": [
    {
      "from": "did:essentia:U",
      "to": "did:essentia:V",
      "depth": 2,
      "cutRule": "newestEdgeDropped"
    }
  ],
  "aggregate": { "resolved": 10234, "overriddenByDirectVote": 412 }
}
```

**Governance RPC (outline)**
`openProposal`, `commitVote`, `revealVote`, `getResult`, `verifyResult`, `setDelegation(domain, toDid)`, `clearDelegation(domain)`

### Appendix C — Verifier CLI (Usage Sketch)

```
essentia-verify \
  --range 1000-1120 \
  --proposal 0xabc... \
  --snapshot 123456 \
  --mode rcv
```

Emits reconstructed eligibility, delegation resolution, round tables, proof checks, and a canonical results JSON whose digest matches chain state.

```
::contentReference[oaicite:0]{index=0}
```
