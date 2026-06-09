# Module 01 - Foundations
 
> Origin of the Stellar network, its founders, the Stellar Development Foundation, key milestones, and the design philosophy behind the protocol.
 
 
## Table of Contents
 
1. [What is Stellar?](#1-what-is-stellar)
2. [Founders and Origin](#2-founders-and-origin)
3. [Technical Origin - The Ripple Fork](#3-technical-origin---the-ripple-fork)
4. [The SCP Breakthrough (2015)](#4-the-scp-breakthrough-2015)
5. [Key Historical Milestones](#5-key-historical-milestones)
6. [The Stellar Development Foundation (SDF)](#6-the-stellar-development-foundation-sdf)
7. [Design Philosophy](#7-design-philosophy)
8. [Stellar vs Other Networks - Positioning](#8-stellar-vs-other-networks---positioning)
9. [Official Resources](#9-official-resources)

 
## 1. What is Stellar?
 
Stellar is an open-source, decentralized payment network designed specifically for one purpose: **moving money across borders quickly, cheaply, and inclusively**. It is not a general-purpose application platform (like Ethereum), nor a store of value (like Bitcoin). From day one, its goal has been to connect the world's financial infrastructure.
 
The network operates through a global set of validator nodes running `stellar-core`, the reference implementation written in C++. Every node stores a shared ledger of all accounts and balances. Transactions are settled not through mining, but through a consensus process based on overlapping sets of trusted peers.
 
> "The Stellar network offers a way to create a more accessible system that lets people participate in a worldwide, stable, financial network regardless of where they live."
> - Stellar Development Foundation
 
**Key properties at a glance:**
 
| Property             | Value                              |
|----------------------|------------------------------------|
| Type                 | Decentralized payment network      |
| Native token         | XLM (Lumens)                       |
| Consensus            | Stellar Consensus Protocol (SCP)   |
| Transaction finality | 3-5 seconds                        |
| Base fee             | 100 stroops (0.00001 XLM)          |
| Open source license  | Apache 2.0                         |
| Core language        | C++                                |
 
 
## 2. Founders and Origin
 
Stellar was co-founded in **2014** by two people with complementary backgrounds:
 
### Jed McCaleb
An American programmer and entrepreneur with a history of building foundational internet infrastructure. Before Stellar, McCaleb had:
- Founded **Mt. Gox** (2010), the first major Bitcoin exchange
- Co-founded **Ripple** (2012), where he developed the original Ripple payment protocol
In 2013, McCaleb departed from Ripple amid disagreements over the company's strategic direction. Ripple was increasingly focused on serving large financial institutions, while McCaleb wanted to build something accessible to anyone - including the unbanked populations of the world. That disagreement became the seed of Stellar.
 
### Joyce Kim
A former lawyer and entrepreneur who co-founded the Stellar Development Foundation alongside McCaleb. Kim served as the first Executive Director of SDF and helped shape its nonprofit structure and early partnerships.
 
### The founding moment
On **July 31, 2014**, the Stellar Development Foundation was publicly announced. The organization launched with a **$3 million loan from Stripe**, the payments company - a meaningful early validation of the project's financial inclusion mission.
 
> Source: [Stellar - Towards Global Economic Change: A Look Back at Stellar's First Year](https://stellar.org/blog/first-year)
 
 
## 3. Technical Origin - The Ripple Fork
 
Stellar's initial codebase was not built from scratch. McCaleb resumed development on the same open-source Ripple protocol code he had originally helped create, adapting it for peer-to-peer payments without intermediaries.
 
This technical origin explains several similarities between the two networks - particularly around the concepts of trust lines and issued assets - that persist to this day.
 
However, in **September 2014**, the network experienced a significant fork (a split in the ledger state), exposing fundamental weaknesses in the inherited Ripple-based consensus protocol. The SDF team declared in December 2014 that it would build an entirely new consensus mechanism from scratch.
 
This decision marked the real beginning of Stellar as an independent protocol.
 
> Source: [Bitcoin Magazine - The New Stellar Consensus Protocol Could Permit Faster And Cheaper Transactions (2015)](https://bitcoinmagazine.com/business/new-stellar-consensus-protocol-permit-faster-cheaper-transactions-1429304217)
 
 
## 4. The SCP Breakthrough (2015)
 
On **April 8, 2015**, Professor **David Mazières** - Chief Scientist at SDF and head of the Secure Computer Systems Group at Stanford University - published the whitepaper for the **Stellar Consensus Protocol (SCP)**, alongside a completely new open-source codebase.
 
The whitepaper, titled *"The Stellar Consensus Protocol: A Federated Model for Internet-Level Consensus"*, introduced a new category of consensus mechanism called **Federated Byzantine Agreement (FBA)**. This was the first provably safe consensus mechanism designed around decentralized, flexible trust - with no mining and no staking required.
 
SCP became operational on the Stellar mainnet in **November 2015**, marking a complete break from the Ripple-derived protocol and establishing Stellar as a fully independent network with its own technical identity.
 
> Source: [Stellar Blog - Stellar Consensus Protocol: Proof and Code](https://stellar.org/blog/foundation-news/stellar-consensus-protocol-proof-code)
 
 
## 5. Key Historical Milestones
 
| Year | Event |
|------|-------|
| **2014** | SDF founded by Jed McCaleb and Joyce Kim (July 31). $3M loan from Stripe. Network launched on Ripple-based protocol. |
| **2014** | Network fork in September exposes weaknesses in consensus. SDF announces plans to build a new protocol. |
| **2015** | SCP whitepaper published by David Mazières (April 8). New codebase released. SCP enters mainnet (November). |
| **2017** | Lightyear.io founded as the for-profit commercial arm of the Stellar ecosystem. |
| **2019** | SDF burns 55 billion XLM, reducing total supply from ~105B to ~50B. Inflation mechanism removed. Denelle Dixon appointed as CEO and Executive Director. |
| **2021** | Circle launches native USDC on Stellar, making it one of the primary networks for dollar-denominated cross-border payments. |
| **2022** | SDF announces the goal of adding smart contract capabilities to the network (Soroban). |
| **2024** | Protocol 20 activates Soroban on mainnet (February 20) - the largest upgrade in Stellar's 10-year history. SDF commits $100M Soroban Adoption Fund. Protocol 21 follows (June 18) with passkey support and cost improvements. |
 
> Sources: [Stellar Protocol Upgrades](https://stellar.org/protocol-upgrades) | [Soroban Launch Announcement](https://stellar.org/press/smart-contracts-launch-on-stellar)
 
 
## 6. The Stellar Development Foundation (SDF)
 
The SDF is a **non-profit organization** incorporated in the U.S. State of Delaware as a non-stock corporation. It has no shareholders, generates no private profit, and has no private inurement. Its legal structure enforces its mission at the organizational level.
 
### Mission
> "Creating equitable access to the global financial system through blockchain technology."
> - [SDF About Page](https://stellar.org/foundation)
 
### What SDF does
- Maintains the `stellar-core` codebase and official SDKs
- Provides strategic direction for the network in collaboration with ecosystem participants
- Funds builders and projects through grants and investment programs
- Represents the network as an independent voice with regulators and institutions
- Operates 3 of the approximately 77 active validator nodes on the network
### What SDF does NOT do
- SDF does not control the network. No single entity does.
- SDF does not own XLM or profit from its price.
- SDF cannot unilaterally change the protocol. Protocol upgrades require a validator vote.
### Leadership (as of 2024)
- **Denelle Dixon** - CEO and Executive Director (since 2019)
- **Jed McCaleb** - Co-founder and Chief Architect (focused on protocol and adoption strategy)
> Source: [SDF Foundation Page](https://stellar.org/foundation) | [SDF Previous Mandate](https://stellar.org/foundation/previous-mandate)
 
 
## 7. Design Philosophy
 
Stellar was designed around a clear set of principles that distinguish it from other blockchain networks:
 
### Financial inclusion first
Every technical decision in Stellar - from fee structure to account minimums to the Anchor model - reflects the goal of making financial services accessible to people regardless of income level, geography, or access to banking infrastructure.
 
### Payments as the primary primitive
Stellar does not try to be a general-purpose computing platform. Its protocol is optimized for one category of operation: moving value between parties. This focus enables the performance and simplicity that payments require.
 
### Open participation
Anyone can run a validator node. Anyone can issue assets. Anyone can build on the network. The open-source codebase and Apache 2.0 license are structural commitments to this principle.
 
### No mining, no waste
Stellar uses SCP, a consensus mechanism that requires no computational competition to produce blocks. This eliminates energy waste and enables deterministic finality in seconds.
 
### Bridging traditional finance
Stellar was designed to work with, not against, existing financial infrastructure. The Anchor model and SEP (Stellar Ecosystem Proposal) standards exist specifically to connect banks, payment processors, and money services to the blockchain layer.
 
 
## 8. Stellar vs Other Networks - Positioning
 
| Network  | Primary Purpose          | Consensus       | Smart Contracts | Designed For              |
|----------|--------------------------|-----------------|-----------------|---------------------------|
| Stellar  | Cross-border payments    | SCP (FBA)       | Yes (Soroban)   | Payments, remittances     |
| XRPL     | Payments, DEX            | RPCA            | Limited         | Payments, institutional   |
| Hedera   | Enterprise DLT           | Hashgraph (aBFT)| Yes (HSCS)      | Enterprise, tokenization  |
| Ethereum | General-purpose platform | PoS (Casper)    | Yes (EVM)       | DeFi, dApps, NFTs         |
| Bitcoin  | Store of value           | PoW             | Limited         | Value transfer, savings   |
 
 
## 9. Official Resources
 
| Resource                          | URL                                                              |
|-----------------------------------|------------------------------------------------------------------|
| SDF Foundation Page               | <https://stellar.org/foundation>                                 |
| SDF Previous Mandate (mission)    | <https://stellar.org/foundation/previous-mandate>                |
| SCP Whitepaper (David Mazières)   | <https://www.stellar.org/papers/stellar-consensus-protocol>      |
| SCP Proof and Code (SDF Blog)     | <https://stellar.org/blog/foundation-news/stellar-consensus-protocol-proof-code> |
| Stellar Protocol Upgrades         | <https://stellar.org/protocol-upgrades>                          |
| Soroban Launch Announcement       | <https://stellar.org/press/smart-contracts-launch-on-stellar>    |
| Stellar First Year Blog           | <https://stellar.org/blog/first-year>                            |
 
---
 
*Module 01 of 07 - Stellar Knowledge Base*  
*Maintained by: [Miguel Fagundez](https://github.com/miguelfagundez) & [Nemorix Group, LLC](https://github.com/nemorixgroup)*
