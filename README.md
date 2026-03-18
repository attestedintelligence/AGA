# AGA - Attested Governance Artifacts

Cryptographic runtime governance for AI agents and autonomous systems.

[![CI](https://github.com/attestedintelligence/AGA/actions/workflows/ci.yml/badge.svg)](https://github.com/attestedintelligence/AGA/actions/workflows/ci.yml)
[![npm](https://img.shields.io/npm/v/@attested-intelligence/aga-mcp-server)](https://www.npmjs.com/package/@attested-intelligence/aga-mcp-server)
[![License: BUSL-1.1](https://img.shields.io/badge/License-BUSL--1.1-blue.svg)](LICENSE)

## What This Does

AGA generates sealed, cryptographically signed Policy Artifacts that bind subject identity to authorized behavior and enforce that binding continuously at runtime. A two-process Portal architecture holds all cryptographic keys while the governed agent holds none - the agent cannot self-authorize, bypass enforcement, or forge receipts.

**Seal. Enforce. Prove.**

## Architecture

```
Policy Author          Portal (Keys)          Subject (Agent)
     |                     |                       |
[Seal Artifact]  ------>  [Store Reference]        |
     |                     |                       |
     |              [Continuous Measurement] <---- [Runtime State]
     |                     |                       |
     |              [Drift Detected?]              |
     |                  /     \                    |
     |            Yes /       \ No                 |
     |               /         \                   |
     |     [Enforce Action]  [Append Receipt]      |
     |            |              |                 |
     |     [Signed Receipt]     |                 |
     |            |              |                 |
     |     [Continuity Chain] <-+                 |
     |            |                               |
     |     [Evidence Bundle]                      |
     |            |                               |
     v     [Offline Verify]                       v
```

### Core Operations

- **Seal:** Attest subject state, compute sealed hash (SHA-256, no delimiters), sign with Ed25519 over RFC 8785 canonical JSON
- **Enforce:** Portal measures runtime state against sealed reference, executes enforcement on drift (7 graduated actions including phantom execution for forensic capture)
- **Prove:** Evidence Bundles with Merkle inclusion proofs enable offline verification by any third party using only standard cryptographic libraries

## Quick Start

```bash
git clone https://github.com/attestedintelligence/AGA.git
cd AGA
npm install
npm test
```

### Run the Independent Verifier

```bash
node independent-verifier/verify.js aga-evidence/evidence-bundle.json
```

### Generate an Evidence Bundle

```bash
node scripts/generate-evidence-bundle.mjs
```

## Deployment Scenarios

| Scenario | Description | Key Feature |
|----------|-------------|-------------|
| Air-Gapped Audit | Portable evidence bundles for DDIL environments | No network connectivity required |
| SCADA/ICS Enforcement | Real-time integrity monitoring for industrial control | O(1) receipt generation, deterministic bounds |
| Autonomous Safe-State | Automatic transition to safe profiles on drift | 7 graduated enforcement actions |
| Model Deployment Gate | Policy-enforced model release to production | Integrity verification before execution |
| Cloud Governance | Cryptographic governance for cloud infrastructure | Evidence isolation, multi-tenant support |
| SOC/IR Evidence | Incident response bundles with chain-of-custody | Forensic-grade audit trails |

## Cryptographic Primitives

| Primitive | Purpose |
|-----------|---------|
| Ed25519 | Digital signatures (artifact sealing, receipt signing) |
| SHA-256 | Content integrity (sealed hash computation) |
| BLAKE2b-256 | Artifact fingerprinting |
| RFC 8785 (JCS) | Canonical JSON serialization |
| HKDF-SHA256 | Key derivation |
| Merkle Trees | Checkpoint anchoring to immutable storage |

## MCP Server

The AGA MCP Server provides AI tool integration for governance operations:

```bash
npm install -g @attested-intelligence/aga-mcp-server
```

Enables AI agents to seal artifacts, verify evidence bundles, and query governance state through the Model Context Protocol.

## Test Suite

112+ automated tests covering the full AGA lifecycle:

```bash
npm test                    # Run all tests
npm run test:coverage       # Coverage report
```

## Project Structure

```
src/               # Core AGA implementation
  sealer/          # Artifact sealing and signing
  portal/          # Runtime enforcement boundary
  chain/           # Continuity chain and receipts
  bundle/          # Evidence Bundle generation
  verifier/        # Programmatic verification
independent-verifier/  # Standalone verification tool
scenarios/         # Deployment scenario implementations
tests/             # Test suite (112+ tests)
scripts/           # Utility scripts
```

## Links

- [Website](https://attestedintelligence.com)
- [Technology](https://attestedintelligence.com/technology)
- [Diligence Materials](https://attestedintelligence.com/diligence)
- [MCP Server (npm)](https://www.npmjs.com/package/@attested-intelligence/aga-mcp-server)

## Security

See [SECURITY.md](SECURITY.md) for vulnerability reporting.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for development setup and guidelines.

## License

[BUSL-1.1](LICENSE) - Business Source License 1.1

---

Attested Intelligence Holdings LLC
