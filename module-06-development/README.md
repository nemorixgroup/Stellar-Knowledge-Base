# Module 06 - Development on Stellar
 
> A technical study of the Stellar developer stack: networks, Stellar Core, the Horizon and RPC data layers, official and community SDKs, the Stellar CLI, Soroban smart contract development, and the broader tooling ecosystem.
 

 
## Table of Contents
 
1. [The Stellar Stack - Core Components](#1-the-stellar-stack---core-components)
2. [Networks - Mainnet, Testnet, Futurenet](#2-networks---mainnet-testnet-futurenet)
3. [Horizon vs Stellar RPC](#3-horizon-vs-stellar-rpc)
4. [SDKs - Officially and Community Supported Languages](#4-sdks---officially-and-community-supported-languages)
5. [Stellar CLI](#5-stellar-cli)
6. [Smart Contract Development (Soroban)](#6-smart-contract-development-soroban)
7. [Testnet and Friendbot](#7-testnet-and-friendbot)
8. [Additional Ecosystem Tools](#8-additional-ecosystem-tools)
9. [Building with AI](#9-building-with-ai)
10. [Official Resources](#10-official-resources)

 
## 1. The Stellar Stack - Core Components
 
The official documentation describes the Stellar stack as four components working together to provide financial infrastructure that is resilient, accessible, and fast enough for real-world use:
 
| Component | Role |
|-----------|------|
| Networks | Mainnet, Testnet, and Futurenet - the environments where the protocol runs |
| Stellar Core | The C++ reference implementation of the protocol, run by every validator |
| Stellar RPC | The service providing real-time access to network data |
| SDKs | Client libraries in multiple languages for building and submitting transactions |
 
SDKs simplify interaction with Horizon and Stellar RPC by converting raw network data into language-native formats, letting developers build in their language of choice without handling XDR encoding manually.
 
> Source: [Stellar Stack - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/stellar-stack)
 
 
## 2. Networks - Mainnet, Testnet, Futurenet
 
Stellar operates three distinct networks, each identified by a unique network passphrase that is included in every signed transaction to prevent cross-network replay:
 
| Network | Also known as | Purpose |
|---------|---------------|---------|
| Mainnet | Pubnet, Public Network | Production network used by live applications with real value |
| Testnet | - | Free test network maintained by SDF, functionally identical to Mainnet but without real value. The recommended environment for development and testing |
| Futurenet | - | Development network for testing bleeding-edge, not-yet-released features |
 
> Source: [Stellar Stack - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/stellar-stack)
 
 
## 3. Horizon vs Stellar RPC
 
### Horizon
 
Horizon has historically been the standard REST API for querying the Stellar ledger: accounts, transactions, operations, offers, and historical data. It remains operational and widely used.
 
### Stellar RPC
 
Stellar RPC (renamed from Soroban-RPC in November 2024) is a lightweight tool providing real-time access to Stellar network data. It is positioned by the SDF as the recommended starting point for new builders coming to the ecosystem.
 
Key characteristics:
- Can be accessed via cURL or any of the official SDKs
- Functions as a drop-in replacement for Horizon in most use cases
- Running your own RPC instance gives full operational control without depending on a third-party provider
### Why two systems coexist
 
Horizon provides indexing features that RPC intentionally does not replicate. The SDF's stated rationale is to leave that indexing and analytics business opportunity to third-party providers rather than competing with them directly.
 
### Infrastructure providers
 
Multiple third-party infrastructure providers offer Stellar RPC services with plans ranging from free tiers to dedicated instances, supporting Mainnet, Testnet, and Futurenet. A specialized "RPC Archive" option exists for retrieving full historical ledger data via the `getLedgers` method.
 
> Source: [Stellar RPC - developers.stellar.org](https://developers.stellar.org/docs/data/apis/rpc)
 
 
## 4. SDKs - Officially and Community Supported Languages
 
Stellar SDKs fall into two categories: client SDKs for building and submitting transactions, and contract SDKs for writing Soroban smart contracts.
 
### Client SDKs
 
| Language | Maintainer | Package / Notes |
|----------|-----------|------------------|
| JavaScript / TypeScript | SDF | `stellar-sdk` - the most widely used SDK; works in browsers and Node.js |
| Java | SDF / community | `java-stellar-sdk` - builds transactions, connects to Horizon and RPC, supports Soroban |
| Go | SDF | Split into packages: `txnbuild` (transaction construction), Horizon Client, RPC Client, Ingest SDK |
| Python | Community | `py-stellar-base` |
| Rust | SDF / community | Direct interaction with the Soroban smart contract environment |
| Swift (iOS / macOS) | Community (Soneso) | `stellar-ios-mac-sdk` |
| Flutter / Dart | Community (Soneso) | `stellar_flutter_sdk` - builds transactions, connects to Horizon, deploys and invokes Soroban contracts |
| PHP | Community (Soneso) | `stellar-php-sdk` |
| Kotlin Multiplatform | Community | `kmp-stellar-sdk` - targets JVM, iOS, macOS, and JavaScript from a single codebase |
 
### Contract SDKs
 
The SDF maintains the official Rust SDK for writing Soroban smart contracts. Rust is currently the primary language for writing contract logic; the client SDKs above are used to interact with already-deployed contracts from any supported language.
 
> Source: [Client SDKs - developers.stellar.org](https://developers.stellar.org/docs/tools/sdks/client-sdks)
 
 
## 5. Stellar CLI
 
The Stellar CLI is the official command-line interface for interacting with the network. Its responsibilities include:
 
- Building, deploying, and invoking Soroban smart contracts
- Configuring identities and generating keypairs
- Managing network configuration (Mainnet, Testnet, Futurenet aliases)
- Wrapping classic Stellar assets as Soroban tokens via the Stellar Asset Contract
### Example deployment flow
 
```bash
stellar contract deploy \
  --wasm contract.wasm \
  --source-account <SECRET_KEY> \
  --rpc-url https://soroban-testnet.stellar.org:443 \
  --network-passphrase 'Test SDF Network ; September 2015'
 
stellar contract invoke \
  --id <CONTRACT_ID> \
  --source-account <SECRET_KEY> \
  --rpc-url https://soroban-testnet.stellar.org:443 \
  --network-passphrase 'Test SDF Network ; September 2015' \
  -- \
  initialize \
  --admin <ADDRESS>
```
 
Configuration (identities, network aliases, persistent settings) is stored in `$XDG_CONFIG_HOME/stellar`, defaulting to `~/.config/stellar` if unset.
 
> Source: [Stellar CLI - developers.stellar.org](https://developers.stellar.org/docs/tools/cli)
 
 
## 6. Smart Contract Development (Soroban)
 
Soroban is the name of Stellar's smart contract platform. Contracts are written in Rust, compiled to WebAssembly (WASM), and that WASM binary is what gets deployed and executed on the network.
 
The official documentation for contract development is organized into eight sections:
 
| Section | Covers |
|---------|--------|
| Overview | Key concepts of Stellar smart contracts |
| Contract Rust Dialect | Differences between Soroban's Rust subset and standard Rust |
| Environment Concepts | The interface of objects, functions, and data available to contracts at runtime |
| Contract Interactions | How to invoke contracts and simulate transactions before submission |
| Types | Soroban's type system for contract values |
| Storage | The three storage types (Persistent, Temporary, Instance) and their rent implications |
| Authorization | Soroban's permission model, distinct from classic multi-sig |
| Contract Lifecycle | Development, deployment, upgrade, and maintenance of contracts |
 
### Transaction simulation
 
Before submitting a smart contract transaction to the network, developers can run it through `simulateTransaction` via Stellar RPC. This dry-run process detects errors and estimates resource costs (CPU instructions, ledger I/O) ahead of actual submission, which is essential given the resource-based fee model described in Module 03.
 
> Source: [Smart Contracts - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/contract-development)
 
 
## 7. Testnet and Friendbot
 
Developing against Testnet requires funded test accounts. The SDF maintains **Friendbot**, a free faucet that funds new Testnet accounts with test XLM:
 
```
https://friendbot.stellar.org?addr=<YOUR_ACCOUNT_ID>
```
 
The funded balance has no real-world value and exists purely to allow free experimentation during development.
 
> Source: [Lumens - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/lumens)
 
 
## 8. Additional Ecosystem Tools
 
Beyond SDKs and the CLI, the SDF and the community maintain a broader set of tools:
 
| Tool | Purpose |
|------|---------|
| Stellar Lab | Web interface for building, signing, and submitting transactions manually without writing code |
| Stellar Expert | Network explorer for accounts, transactions, assets, and liquidity pools |
| Stellar Dashboard | Live network metrics including supply breakdown and fee pool data |
| Hubble | SDF's data warehouse for advanced historical network analytics |
| Scaffold Stellar | Project scaffolding tool to bootstrap new Stellar applications quickly |
| Freighter | The officially recommended browser wallet extension for dApp testing and development |
| OpenZeppelin Relayer & Contracts | OpenZeppelin's transaction relayer and contract libraries, extended to support Soroban |
 
> Source: [Developer Tools - developers.stellar.org](https://developers.stellar.org/docs/tools/developer-tools)
 
 
## 9. Building with AI
 
The SDF maintains official resources to help AI coding assistants work effectively with Stellar:
 
**Stellar Development Skill** - an Agent Skills-compatible package that gives AI coding tools deep knowledge of Soroban smart contracts, the JavaScript/Python/Go client SDKs, RPC and Horizon APIs, wallet integration (Freighter, Stellar Wallets Kit), passkey-based smart accounts, testing strategies, and agentic payment concepts such as x402 and the Machine Payments Protocol (MPP).
 
**llms.txt** - a structured, machine-readable overview of Stellar's developer documentation, designed to be added as context to any LLM-based tool.
 
This same principle, structured and verifiable documentation prepared for consumption, is the foundation of this knowledge base.
 
> Source: [Building with AI - developers.stellar.org](https://developers.stellar.org/docs/tools/developer-tools/building-with-ai)
 
 
## 10. Official Resources
 
| Resource | URL |
|----------|-----|
| Stellar Stack Overview | <https://developers.stellar.org/docs/learn/fundamentals/stellar-stack> |
| Stellar RPC | <https://developers.stellar.org/docs/data/apis/rpc> |
| RPC Providers | <https://developers.stellar.org/docs/data/apis/rpc/providers> |
| Horizon | <https://developers.stellar.org/docs/data/apis/horizon> |
| Client SDKs | <https://developers.stellar.org/docs/tools/sdks/client-sdks> |
| Stellar CLI | <https://developers.stellar.org/docs/tools/cli> |
| Smart Contracts Overview | <https://developers.stellar.org/docs/learn/fundamentals/contract-development> |
| Developer Tools | <https://developers.stellar.org/docs/tools/developer-tools> |
| Building with AI | <https://developers.stellar.org/docs/tools/developer-tools/building-with-ai> |
| Stellar Lab | <https://lab.stellar.org> |
| Stellar Expert | <https://stellar.expert> |
| Stellar Dashboard | <https://dashboard.stellar.org> |
| Flutter SDK (Soneso) | <https://github.com/Soneso/stellar_flutter_sdk> |
 
---
 
*Module 06 of 07 - Stellar Knowledge Base*  
*Maintained by: [Miguel Fagundez](https://github.com/miguelfagundez) & [Nemorix Group, LLC](https://github.com/nemorixgroup)*
