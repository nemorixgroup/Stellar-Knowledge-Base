# Stellar Knowledge Base
 
> A comprehensive, in-depth technical guide to the Stellar network - covering its architecture, consensus protocol, native services, token economics, development ecosystem, and real-world use cases.
 
## About This Repository
 
This repository is a structured knowledge base built for developers, researchers, and engineers who want to understand Stellar at a deep technical level - not just at a surface level.
 
The content is organized in progressive modules, starting from foundational concepts and advancing toward hands-on development. Each module is self-contained and can be studied independently, but reading them in order is recommended for a complete understanding.
 
**Who is this for?**
 
- Developers exploring the Stellar network for the first time
- Engineers evaluating Stellar for payments, remittances, or tokenization projects
- Blockchain enthusiasts who want to understand how SCP differs from other consensus mechanisms
- Anyone building financial applications for cross-border payments and financial inclusion  

**Languages:** English (primary). Some modules may include Spanish summaries.
 
## Repository Structure
 
```
stellar-knowledge-base/
├── README.md                              <- You are here (General Index)
├── module-01-foundations/
│   └── README.md                          <- History, Jed McCaleb, SDF, mission
├── module-02-consensus/
│   └── README.md                          <- SCP, FBA, Quorum Slices, validators, finality
├── module-03-xlm-token/
│   └── README.md                          <- Tokenomics, supply, fees, base reserve
├── module-04-accounts-transactions/
│   └── README.md                          <- Accounts, operations, sequence numbers, lifecycle
├── module-05-native-services/
│   └── README.md                          <- DEX, Path Payments, Liquidity Pools, Anchors, SEPs
├── module-06-development/
│   └── README.md                          <- SDKs, Horizon API, Soroban, Testnet, dev tools
└── module-07-ecosystem/
    └── README.md                          <- Remittances, USDC, adoption, comparisons, roadmap
```
 
## Modules
 
| #  | Module                                                                    | Topics                                                                  |
|----|---------------------------------------------------------------------------|-------------------------------------------------------------------------|
| 01 | [Foundations & History](./module-01-foundations/README.md)                | Origin, Jed McCaleb, SDF, open-source mission, design philosophy        |
| 02 | [Consensus Protocol (SCP)](./module-02-consensus/README.md)               | SCP, FBA, Quorum Slices, Quorum Sets, validators, finality              |
| 03 | [XLM Token](./module-03-xlm-token/README.md)                             | Tokenomics, fixed supply, fees, base reserve, anti-spam mechanism       |
| 04 | [Accounts & Transactions](./module-04-accounts-transactions/README.md)    | Accounts, operations, sequence numbers, transaction lifecycle, multi-sig |
| 05 | [Native Services](./module-05-native-services/README.md)                  | DEX, Path Payments, Liquidity Pools, Anchors, SEP standards             |
| 06 | [Development on Stellar](./module-06-development/README.md)               | SDKs, Horizon API, Soroban smart contracts, Testnet, dev tools          |
| 07 | [Ecosystem & Future](./module-07-ecosystem/README.md)                     | Remittances, USDC on Stellar, adoption, comparisons, roadmap            |
 
## Official Resources
 
### Core
 
| Resource                    | URL                                          |
|-----------------------------|----------------------------------------------|
| Official Website            | <https://stellar.org>                        |
| Developer Documentation     | <https://developers.stellar.org/docs>        |
| Stellar Whitepapers         | <https://stellar.org/papers>                 |
| Stellar Blog                | <https://stellar.org/blog>                   |
| Stellar Foundation          | <https://stellar.org/foundation>             |
 
### Developer Tools
 
| Resource                          | URL                                                      |
|-----------------------------------|----------------------------------------------------------|
| Stellar Expert (Network Explorer) | <https://stellar.expert>                                 |
| Stellar Laboratory                | <https://laboratory.stellar.org>                         |
| Horizon API (Mainnet)             | <https://horizon.stellar.org>                            |
| Stellar Faucet (Testnet)          | <https://friendbot.stellar.org>                          |
| Soroban IDE (online)              | <https://stellar.org/developers/soroban>                 |
 
### SDKs (Official)
 
| Language                | Repository                                                    |
|-------------------------|---------------------------------------------------------------|
| JavaScript / TypeScript | <https://github.com/stellar/js-stellar-sdk>                   |
| Python                  | <https://github.com/StellarCN/py-stellar-base>                |
| Java                    | <https://github.com/stellar/java-stellar-sdk>                 |
| Go                      | <https://github.com/stellar/go>                               |
| Swift                   | <https://github.com/Soneso/stellar-ios-mac-sdk>               |
| Flutter / Dart          | <https://github.com/Soneso/stellar_flutter_sdk> *(community)* |
 
### GitHub Organizations
 
| Organization                   | URL                                           |
|--------------------------------|-----------------------------------------------|
| Stellar (core)                 | <https://github.com/stellar>                  |
| Stellar Docs                   | <https://github.com/stellar/stellar-docs>     |
| Soroban Examples               | <https://github.com/stellar/soroban-examples> |
 
### Community & News
 
| Resource            | URL                                      |
|---------------------|------------------------------------------|
| Stellar Blog        | <https://stellar.org/blog>               |
| Stellar Discord     | <https://discord.gg/stellardev>          |
| Stellar Twitter / X | <https://twitter.com/stellarorg>         |
| Stellar Reddit      | <https://reddit.com/r/stellar>           |
| Stellar Developers  | <https://developers.stellar.org>         |
 
## Key Technical Facts (Quick Reference)
 
| Property                | Value                                                   |
|-------------------------|---------------------------------------------------------|
| Initial Release         | July 2014                                               |
| Consensus Algorithm     | Stellar Consensus Protocol (SCP)                        |
| Transaction Finality    | 3-5 seconds                                             |
| Throughput              | ~1,000 TPS (sustained)                                  |
| Base Transaction Fee    | 100 stroops (0.00001 XLM)                               |
| Native Token            | XLM (Lumens)                                            |
| Total Supply            | ~50,000,000,000 XLM (fixed after 2019 burn)             |
| Energy Usage            | No mining - negligible energy consumption               |
| Native DEX              | Yes - built into the protocol                           |
| Smart Contracts         | Yes - Soroban (Rust-based, launched 2024)               |
| Open Source             | Yes (Apache 2.0)                                        |
| Core Implementation     | stellar-core (C++)                                      |
 
## Why Stellar?
 
The Stellar network was designed from the ground up for one purpose: **moving money across borders quickly, cheaply, and inclusively**. Several properties make it technically distinctive:
 
**Novel consensus.** Stellar uses the Stellar Consensus Protocol (SCP) - the first provably safe consensus mechanism based on Federated Byzantine Agreement (FBA). Unlike proof-of-work or proof-of-stake systems, SCP achieves agreement through overlapping trust relationships called Quorum Slices, with no mining and no staking required.
 
**Built for payments.** The Stellar protocol natively supports multi-currency payments through Path Payments - a mechanism that automatically finds the best conversion route between assets using the built-in DEX and Liquidity Pools. A sender can pay in one currency while the receiver receives another, settled in seconds.
 
**Anchors and real-world assets.** Stellar's Anchor system allows regulated financial institutions to issue tokenized versions of fiat currencies directly on the network, following the SEP (Stellar Ecosystem Proposal) standards. This bridges traditional finance with blockchain infrastructure.
 
**USDC native support.** Circle's USDC is natively issued on Stellar, making it one of the primary networks for dollar-denominated remittances and cross-border payments at minimal cost.
 
**Financial inclusion by design.** Stellar's architecture - with fees as low as $0.00001 per transaction and finality in seconds - was specifically designed to serve the unbanked and underbanked populations in developing economies.
 
This is not just another blockchain. The Stellar network is purpose-built financial infrastructure where asset issuance, currency exchange, and cross-border settlement are unified at the protocol level.
 
## Important Distinctions
 
| Concept      | Description                                                                                          |
|--------------|------------------------------------------------------------------------------------------------------|
| **XLM**      | The native digital asset of the Stellar network (also called Lumens)                                 |
| **Stellar**  | The open-source, decentralized payment network                                                       |
| **SDF**      | Stellar Development Foundation - a non-profit that supports development of the Stellar network       |
| **Soroban**  | Stellar's smart contract platform, launched in 2024, based on Rust and the WebAssembly runtime      |
| **Anchor**   | A regulated entity that bridges fiat currency to the Stellar network by issuing tokenized assets     |
| **SEP**      | Stellar Ecosystem Proposals - open standards for interoperability between Stellar-based applications |
 
---
 
## About This Guide
 
This knowledge base is built from official Stellar documentation, technical papers published at stellar.org, and verified network data. No speculative content or price predictions are included.
 
The goal is to provide a clear, honest, and technically accurate resource for understanding the Stellar network from the inside out.
 
---
 
*Last updated: June 2026*  
*Maintained by: [Miguel Fagundez](https://github.com/miguelfagundez) & [Nemorix Group, LLC](https://github.com/nemorixgroup)*
