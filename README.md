# KeeperNet SDKs

The official client libraries for interacting with the KeeperNet automation protocol on the Stellar network.

The KeeperNet SDK repository contains production-ready packages across multiple languages, allowing developers to seamlessly integrate automated Soroban smart contract triggers, gas-aware relaying, and execution simulations into any tech stack.

[![View Packages](https://img.shields.io/badge/Packages-View%20All-blue)](/)
[![SDK Documentation](https://img.shields.io/badge/Docs-Read%20Now-blue)](/docs)
[![Contributing](https://img.shields.io/badge/Contributing-Welcome-green)](/CONTRIBUTING.md)
[![License](https://img.shields.io/badge/License-MIT-purple)](LICENSE)

---

## Core Design Principles

Across all supported languages, the SDKs adhere to a strict, unified design philosophy to ensure a consistent developer experience:

| Principle | Description |
|-----------|-------------|
| **Dual-Layer Architecture** | Every SDK provides both high-level convenience methods (e.g., `registerJob()`) and low-level transaction builders for fine-grained XDR manipulation |
| **Universal Mock Mode** | Built-in `mockMode` allows developers to unit-test their automation pipelines offline without touching a local Soroban RPC or testnet |
| **Structured Error Handling** | Protocol-specific Soroban error codes are automatically parsed into native, typed exceptions and errors for each language |
| **Gas Estimation Native** | Integrated simulation endpoints fetch expected gas costs before committing to a job registration |

---

## Supported Languages

| Language | Status | Environment | Distribution |
|----------|--------|-------------|--------------|
| TypeScript / JavaScript | Under Development | Node.js, Browser, Edge (Next.js / Cloudflare) | npm (`@keepernet/sdk`) |
| Rust | Under Development | Server-side, WASM | crates.io (`keeper-net-sdk`) |
| Python | Under Development | Python 3.10+ | PyPI (`keepernet-sdk`) |
| Golang | Under Development | Go 1.21+ | `go get github.com/keepernet/sdk/go` |
| Java | Under Development | Java 17+, Android | Maven Central (`org.keepernet.sdk`) |

### Language Highlights

**TypeScript / JavaScript**
- First-class React hooks support
- Fully typed payload schemas
- Compatible with Node.js, browser, and edge runtimes

**Rust**
- Thread-safe `AsyncClient`
- Zero-copy XDR parsing
- Highly performant with WASM support

**Python**
- `asyncio` native
- Optimized for backend scripts, data engineering, and AI trigger pipelines

**Golang**
- High-concurrency goroutine support
- Minimal memory footprint for microservices

**Java**
- Enterprise-grade type safety
- Spring Boot auto-configuration support
- Android compatible

---

## SDK Directory Structure

```text
sdk/
├── ts/              # TypeScript & JS implementation (npm)
├── rust/            # Rust crate implementation (cargo)
├── python/          # Python asyncio implementation (pip)
├── go/              # Golang module implementation (go mod)
├── java/            # Java implementation (Maven/Gradle)
├── core-schema/     # Language-agnostic XDR definitions and JSON schemas
└── tests/           # Cross-language integration test suite
```

### About `core-schema/`

`core-schema/` is the single source of truth for the entire SDK monorepo. All language implementations derive their types and schemas from here rather than defining them independently. It contains:

- XDR definitions (Stellar's binary format)
- JSON schemas for all request and response payloads
- Shared error code constants
- Contract ABI specifications

Any change to the core protocol logic must be reflected in `core-schema/` first before being propagated to individual SDK implementations.

---

## Quick Start

While syntax varies by language, the operational flow is identical across all SDKs:

1. **Initialize the Client** — connect to the Soroban network and configure your credentials
2. **Build the Trigger** — define when the contract should execute (block height, time, or state)
3. **Simulate and Submit** — run an offline simulation, then broadcast the registration to the KeeperNet relayer

### TypeScript

```typescript
import { KeeperNetClient } from '@keepernet/sdk';

const client = new KeeperNetClient({
  rpcUrl: 'https://soroban-testnet.stellar.org',
  networkPassphrase: 'Test SDF Network ; September 2015',
  mockMode: false,
});

const txId = await client.registerJob({
  targetContract: 'C...',
  functionName: 'rebalance_pool',
  trigger: { type: 'BlockHeight', value: 150000 },
});
```

### Rust

```rust
use keeper_net_sdk::{KeeperNetClient, Config, JobParams, Trigger};

let client = KeeperNetClient::new(Config {
    rpc_url: "https://soroban-testnet.stellar.org".to_string(),
    network_passphrase: "Test SDF Network ; September 2015".to_string(),
    mock_mode: false,
});

let tx_id = client.register_job(JobParams {
    target_contract: "C...".to_string(),
    function_name: "rebalance_pool".to_string(),
    trigger: Trigger::BlockHeight(150000),
}).await?;
```

### Python

```python
from keepernet_sdk import KeeperNetClient, JobParams, BlockHeightTrigger

client = KeeperNetClient(
    rpc_url="https://soroban-testnet.stellar.org",
    network_passphrase="Test SDF Network ; September 2015",
    mock_mode=False,
)

tx_id = await client.register_job(JobParams(
    target_contract="C...",
    function_name="rebalance_pool",
    trigger=BlockHeightTrigger(150000),
))
```

### Golang

```go
package main

import "github.com/keepernet/sdk/go/keepernet"

func main() {
    client := keepernet.NewClient(keepernet.Config{
        RPCUrl:            "https://soroban-testnet.stellar.org",
        NetworkPassphrase: "Test SDF Network ; September 2015",
        MockMode:          false,
    })

    txId, err := client.RegisterJob(keepernet.JobParams{
        TargetContract: "C...",
        FunctionName:   "rebalance_pool",
        Trigger:        keepernet.BlockHeightTrigger(150000),
    })
}
```

### Java

```java
import org.keepernet.sdk.KeeperNetClient;
import org.keepernet.sdk.JobParams;
import org.keepernet.sdk.trigger.BlockHeightTrigger;

KeeperNetClient client = KeeperNetClient.builder()
    .rpcUrl("https://soroban-testnet.stellar.org")
    .networkPassphrase("Test SDF Network ; September 2015")
    .mockMode(false)
    .build();

String txId = client.registerJob(JobParams.builder()
    .targetContract("C...")
    .functionName("rebalance_pool")
    .trigger(new BlockHeightTrigger(150000))
    .build());
```

---

## Cross-Language Integration Tests

The `tests/` directory contains a language-agnostic integration test suite that fires all SDK implementations against the same local contract instance and asserts identical behaviour across every client.

```bash
# Run the full cross-language test suite
cd tests
npm run test:all

# Run a single language
npm run test:ts
npm run test:rust
npm run test:python
npm run test:go
npm run test:java
```

All SDKs must pass the cross-language suite before any release is tagged.

---

## Security

- All SDKs use the official Stellar cryptographic libraries for their respective languages to handle Ed25519 signing
- No private keys are ever transmitted to KeeperNet — the SDKs only build and sign XDR envelopes locally
- Simulation is always run before submission to catch Soroban panics before they cost gas

---

## Roadmap

### Planned

- [ ] `core-schema/` XDR definitions and JSON schema specifications
- [ ] TypeScript SDK — initial implementation and npm package setup
- [ ] Rust SDK — initial crate implementation and crates.io publishing
- [ ] Python SDK — asyncio client and PyPI package setup
- [ ] Golang SDK — goroutine-safe client and Go module setup
- [ ] Java SDK — Maven Central publishing and Spring Boot auto-configuration
- [ ] Universal mock mode across all language implementations
- [ ] Structured error handling mapped from Soroban error codes
- [ ] Native gas estimation and simulation integration
- [ ] Cross-language integration test suite
- [ ] Code generation pipeline from `core-schema/` to all language type systems
- [ ] React hooks package (`@keepernet/react`)
- [ ] Full SDK documentation site

---

## Contributing

Because we maintain multi-language SDKs, keeping feature parity across all implementations is critical.

### Guidelines

- Propose any new API design in an issue before starting implementation
- All changes to core protocol logic must first be reflected in `core-schema/`
- New features must be accompanied by entries in the cross-language integration test suite to ensure consistent behaviour across all clients
- Each language SDK follows its own idiomatic conventions — match the style of the language you are contributing to

### Quick Start for Contributors

1. **Find an issue** — Check `good first issues` or `help wanted` on the issues board
2. **Read the guide** — See [CONTRIBUTING.md](CONTRIBUTING.md)
3. **Pick a language** — Navigate to the relevant SDK subdirectory and follow its local setup instructions
4. **Make your changes** — Create a feature branch
5. **Test** — Ensure both the language-specific tests and the cross-language suite pass
6. **Submit a PR** — Open a pull request with a clear description of what changed and which SDKs are affected

---

## Community and Support

- **Documentation** — [Full Docs](/docs)
- **Issues** — [Report bugs or request features](../../issues)
- **Discussions** — [Stellar Community Forum](https://community.stellar.org)

---

## License

MIT License — Copyright (c) 2026 KeeperNet Protocol.

---

*Automating the future of Soroban, one block at a time.*
