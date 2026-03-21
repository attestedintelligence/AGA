# AGA - Attested Governance Artifacts

Cryptographic runtime governance for AI agents and autonomous systems.

[![CI](https://github.com/attestedintelligence/AGA/actions/workflows/ci.yml/badge.svg)](https://github.com/attestedintelligence/AGA/actions/workflows/ci.yml)
[![npm](https://img.shields.io/npm/v/@attested-intelligence/aga-mcp-server)](https://www.npmjs.com/package/@attested-intelligence/aga-mcp-server)
[![PyPI](https://img.shields.io/pypi/v/aga-governance)](https://pypi.org/project/aga-governance/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

## What This Does

Every tool call an AI agent makes passes through the AGA gateway. Each call is evaluated against policy, and the decision (PERMITTED or DENIED) is recorded as a signed, hash-linked governance receipt. Receipts are collected into evidence bundles that any third party can verify offline using standard cryptography.

**Record. Prove. Verify.**

## Quick Start

### Verify an evidence bundle (3 commands)

```bash
pip install aga-governance
curl -s https://aga-mcp-gateway.attestedintelligence.workers.dev/bundle -o evidence-bundle.json
python -m aga verify evidence-bundle.json
```

### Or verify in your browser

Go to [attestedintelligence.com/verify](https://attestedintelligence.com/verify) and click "Run Verification." Zero installs required.

## How It Works

```
AI Agent                  AGA Gateway                    Verifier
   |                          |                              |
   |-- tools/call ----------->|                              |
   |                    [Evaluate Policy]                    |
   |                    [Sign Receipt]                       |
   |                    [Chain to Previous]                  |
   |<-- PERMITTED/DENIED -----|                              |
   |                          |                              |
   |                    [Export Bundle]                       |
   |                          |--------- evidence.json ----->|
   |                          |                  [Verify Signatures]
   |                          |                  [Verify Chain]
   |                          |                  [Verify Merkle Tree]
   |                          |                  [PASS / FAIL]
```

## Verification (5 steps)

1. **Algorithm Check** - Bundle declares Ed25519-SHA256-JCS, fail closed on anything else
2. **Receipt Signatures** - Ed25519 over RFC 8785 canonical JSON (signature field excluded)
3. **Chain Integrity** - Each receipt's `previous_receipt_hash` = SHA-256 of the preceding receipt
4. **Merkle Proofs** - Walk siblings/directions to root, compare against bundle root
5. **Bundle Consistency** - Proof count = receipt count, leaf hashes match receipt hashes

## Cryptographic Primitives

| Primitive | Purpose |
|-----------|---------|
| Ed25519 | Receipt signatures |
| SHA-256 | Hash chaining, Merkle trees, leaf computation |
| RFC 8785 (JCS) | Canonical JSON for deterministic signing |
| Merkle Trees | Binding all receipts to a single verifiable root |

## Live Gateway

The demo gateway is deployed on Cloudflare Workers:

```bash
# Check status
curl https://aga-mcp-gateway.attestedintelligence.workers.dev/health

# Export evidence bundle
curl https://aga-mcp-gateway.attestedintelligence.workers.dev/bundle -o evidence-bundle.json
```

## Python SDK

```bash
pip install aga-governance
```

```python
from aga import AgentSession

with AgentSession(gateway_id="my-gateway") as session:
    session.record_tool_call(
        tool_name="search_web",
        decision="PERMITTED",
        reason="tool in allowlist",
        request_id="req-1",
    )
    bundle = session.export_bundle()
    result = session.verify()
    assert result["overall_valid"]
```

## TypeScript MCP Gateway

```bash
npm install
npm test    # 94 tests
```

## Test Suite

231+ automated tests across TypeScript and Python:

- **TypeScript MCP Gateway:** 94 tests (vitest)
- **Python SDK:** 137 tests (pytest)
- **Cross-language test vectors:** 37 vectors across 9 categories

```bash
npm test                    # TypeScript gateway tests (94)
pip install aga-governance  # Python SDK from PyPI
python -m aga verify evidence-bundle.json  # CLI verification
```

## Project Structure

```
src/                       # TypeScript MCP gateway
independent-verifier/      # Standalone verification tool
tests/                     # TypeScript test suite
scenarios/                 # Deployment scenario examples
scripts/                   # Utility scripts
```

## Links

- [Website](https://attestedintelligence.com)
- [Live Verifier](https://attestedintelligence.com/verify)
- [Developer Resources](https://attestedintelligence.com/evaluate)
- [Technology](https://attestedintelligence.com/technology)
- [Trust and Scope](https://attestedintelligence.com/trust)
- [Python SDK (PyPI)](https://pypi.org/project/aga-governance/)
- [MCP Server (npm)](https://www.npmjs.com/package/@attested-intelligence/aga-mcp-server)

## Security

See [SECURITY.md](SECURITY.md) for vulnerability reporting.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for development setup and guidelines.

## License

[MIT](LICENSE)

---

Attested Intelligence Holdings LLC
