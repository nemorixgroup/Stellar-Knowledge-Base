# Module 05 - Native Services
 
> A deep technical study of the services built directly into the Stellar protocol: the Stellar Decentralized Exchange (SDEX), Liquidity Pools, Path Payments, Anchors, and the SEP (Stellar Ecosystem Proposals) interoperability standards.
 
 
## Table of Contents
 
1. [Overview - What Makes These Services Native?](#1-overview---what-makes-these-services-native)
2. [SDEX - Stellar Decentralized Exchange](#2-sdex---stellar-decentralized-exchange)
3. [Liquidity Pools and AMMs](#3-liquidity-pools-and-amms)
4. [Path Payments](#4-path-payments)
5. [Anchors - The Bridge to Traditional Finance](#5-anchors---the-bridge-to-traditional-finance)
6. [SEPs - Stellar Ecosystem Proposals](#6-seps---stellar-ecosystem-proposals)
7. [How It All Fits Together](#7-how-it-all-fits-together)
8. [Official Resources](#8-official-resources)

 
## 1. Overview - What Makes These Services Native?
 
The SDEX, Liquidity Pools, and Path Payments are not smart contracts or third-party applications running on top of Stellar. They are implemented directly inside the Stellar Core protocol and are part of the ledger itself. This means:
 
- They inherit the same security guarantees as the rest of the network
- They benefit from the same 3-5 second finality
- They require no separate deployment or maintenance
- They are available to every account on the network by default
Anchors and SEPs operate at the application layer on top of these protocol primitives, but they follow open standards defined by the SDF and the Stellar community, ensuring interoperability across the entire ecosystem.
 
 
## 2. SDEX - Stellar Decentralized Exchange
 
The Stellar Decentralized Exchange (SDEX) is an exchange built directly into the Stellar protocol. Every account on the network can place and manage orders against any asset pair without using any external infrastructure.
 
### How Order Books Work
 
The Stellar ledger stores both account balances and active orders. Each asset pair has its own order book - a record of all outstanding buy and sell orders between those two assets.
 
Orders on Stellar behave like limit orders in traditional markets:
- If the submitted order crosses an existing counter-order, it executes immediately at the existing price
- If no matching counter-order exists, the order is saved in the ledger until filled, cancelled, or consumed by a path payment
Orders are executed on **price-time priority**: best price first, and for equal prices, the older order is filled before the newer one.
 
### Order Types
 
| Operation                 | Description                                                                               |
|---------------------------|-------------------------------------------------------------------------------------------|
| `Manage Buy Offer`        | Creates or updates an order to buy a specific asset using another asset                   |
| `Manage Sell Offer`       | Creates or updates an order to sell a specific asset in exchange for another               |
| `Create Passive Sell Offer` | Creates a sell order that does not execute against a counter-order at the same price     |
 
**Passive orders** allow markets to have zero spread. They are useful for stablecoin pairs where two assets represent the same real-world value (for example, USD from Anchor A and USD from Anchor B), where a direct cross at 1:1 is not desirable.
 
### Obligations and Guarantees
 
When an account creates an order, the selling asset is held as an obligation in the account or trustline. Any operation that would cause the account to be unable to fulfill its obligations will fail. This guarantees that every order in the ledger can be fully executed when matched.
 
### Fees
 
The network fee for submitting an order is the standard inclusion fee (minimum 100 stroops per operation), paid in XLM. This fee is completely separate from the price of the order itself.
 
> Source: [SDEX - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/liquidity-on-stellar-sdex-liquidity-pools)
 
 
## 3. Liquidity Pools and AMMs
 
Liquidity Pools are a second source of liquidity on the Stellar network, operating alongside the SDEX order books. They use an Automated Market Maker (AMM) model rather than discrete orders.
 
### Constant Product AMM
 
Stellar implements a **constant product market maker**. Every pool holds two assets, and the protocol enforces the following invariant at all times:
 
```
Reserve A * Reserve B = k  (constant)
```
 
The exchange rate is determined by the ratio of reserves. A trade that changes the ratio causes the price to move accordingly. Larger pools move less in price per trade because the constant product invariant has a larger base.
 
### Example
 
Pool reserves: 1,000 EUR and 1,170 USD. Product = 1,170,000.
 
Selling 1 EUR for 1.18 USD is acceptable: new reserves would be 999 EUR and 1,171.18 USD, product = 1,170,008.82 (increased).
 
Selling 1 EUR for 1.16 USD is not acceptable: new reserves would be 999 EUR and 1,171.16 USD, product = 1,169,988.84 (decreased, which violates the invariant).
 
Arbitrageurs naturally push the pool's exchange rate back toward the true market rate when these diverge.
 
### Pool Fee
 
The AMM fee on Stellar is fixed at **30 bps (0.30%)** of the amount bought by the AMM. This fee is:
- Charged on every trade through the pool
- Distributed to liquidity providers proportional to their pool share
- Completely separate from the Stellar network transaction fee
The fee is factored into the constant product invariant, so the product of the reserves grows after every trade.
 
### Liquidity Pool Participation
 
Any eligible account can deposit into a pool and receive **pool shares** representing its proportional ownership:
 
```
If total pool shares = 150 and you own 30 shares,
you are entitled to withdraw 20% of both reserves at any time.
```
 
Important constraints for pool participation:
 
- Only **one pool can exist per asset pair** across the entire network
- Pool shares cannot be transferred between accounts
- A pool share trustline requires **2 base reserves** (instead of 1 for a regular trustline)
- The pool ID is deterministic and can be calculated locally from the asset pair parameters
To participate, an account needs trustlines to:
1. Asset A in the pair (unless it is XLM)
2. Asset B in the pair (unless it is XLM)
3. The pool share asset itself
### Authorization and Automatic Withdrawal
 
If the issuer of Asset A or Asset B in a pool revokes authorization, the protocol automatically withdraws the affected account from every pool containing that asset. The withdrawn funds are placed in a Claimable Balance for the account to claim.
 
### Operations
 
| Operation               | Description                                     |
|-------------------------|-------------------------------------------------|
| `LiquidityPoolDeposit`  | Deposit assets into a pool to receive shares    |
| `LiquidityPoolWithdraw` | Burn shares to withdraw the underlying reserves |
 
Accounts that only want to trade (not provide liquidity) do not need these operations. They access pool pricing through Path Payments automatically.
 
> Source: [SDEX and Liquidity Pools - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/liquidity-on-stellar-sdex-liquidity-pools)
 
 
## 4. Path Payments
 
Path Payments are one of the most powerful features of the Stellar protocol. They allow the **sender to pay in one asset** while the **receiver receives a different asset**, with all currency conversions handled automatically and atomically within a single transaction.
 
### How Path Payments Work
 
The Stellar Core pathfinding engine evaluates available routes across the SDEX order books and liquidity pools to find the best conversion path. A payment can cross through multiple hops:
 
```
Sender sends XLM -> [XLM/USDC pool] -> [USDC/MXN order book] -> Receiver gets MXN
```
 
All conversions happen atomically: if any hop in the path fails, the entire transaction fails and no funds are moved. There is no partial settlement, and funds cannot be stranded mid-path.
 
### The Two Path Payment Operations
 
| Operation                   | Who controls the amount  | Use case                                                 |
|-----------------------------|--------------------------|----------------------------------------------------------|
| `PathPaymentStrictSend`     | Sender fixes the amount sent | The sender knows exactly how much to spend; receiver gets whatever arrives after conversions |
| `PathPaymentStrictReceive`  | Sender fixes the amount received | The sender guarantees a specific amount to the receiver; pays up to a declared maximum |
 
Both operations accept an optional explicit path parameter. If no path is provided, Stellar Core finds the best available path automatically.
 
### When Path Payments Fail
 
A path payment fails if no viable exchange path exists between the source and destination assets at the time of execution. This is a protocol-level guarantee: the transaction fails cleanly rather than executing at an unfavorable rate.
 
### Relevance for Remittances
 
Path Payments are the core mechanism enabling cross-currency remittances on Stellar at near-zero cost. A user can send USD in the US and a recipient in Mexico receives MXN, with the conversion happening on-chain in 3-5 seconds, without either party needing to understand the underlying mechanics.
 
> Source: [Path Payments - developers.stellar.org](https://developers.stellar.org/docs/build/guides/transactions/path-payments)
 
 
## 5. Anchors - The Bridge to Traditional Finance
 
An Anchor is the Stellar-specific term for the on/off-ramps that connect the Stellar network to traditional financial infrastructure. Anchors can be banks, fintech companies, payment processors, remittance companies, or any regulated entity operating on Stellar.
 
### What an Anchor Does
 
An Anchor accepts deposits of fiat currency (such as USD, MXN, COP, NGN) via traditional rails (bank transfer, cash-in points, mobile money) and issues the equivalent digital tokens on the Stellar network. The process also works in reverse: token holders can redeem tokens with the Anchor to receive fiat through traditional payment rails.
 
Anchors have two options for asset issuance:
- Issue their own custom asset on the Stellar network (for example: USDX issued by AnchorX)
- Operate with assets that already exist on the network (for example: USDC issued by Circle)
### SDF Anchor Platform
 
The SDF maintains the **Anchor Platform**, an open-source framework that makes it easier to deploy an anchor service compatible with the relevant SEP standards. It handles the protocol layer, letting anchor operators focus on business and compliance logic.
 
### Anchor Directory
 
The SDF maintains a public directory of operating Stellar anchors: [anchors.stellar.org](https://anchors.stellar.org)
 
> Source: [Anchors - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/anchors)
 
 
## 6. SEPs - Stellar Ecosystem Proposals
 
SEPs (Stellar Ecosystem Proposals) are publicly-created, open-source documents that define standards for how different participants in the Stellar ecosystem (anchors, wallets, exchanges, asset issuers, validators) interact and interoperate. They live in the [stellar/stellar-protocol](https://github.com/stellar/stellar-protocol/tree/master/ecosystem) GitHub repository.
 
Without SEPs, every wallet would need a custom integration with every anchor. SEPs define both sides of an interaction once, and any wallet that implements a SEP can work with any anchor that implements the same SEP.
 
SEPs differ from CAPs (Core Advancement Proposals), which propose changes to the Stellar core protocol itself. SEPs operate at the application layer.
 
### Key SEPs for Payments and Remittances
 
| SEP    | Title                                  | Purpose                                                                                      | Used By                          |
|--------|----------------------------------------|----------------------------------------------------------------------------------------------|----------------------------------|
| SEP-1  | Stellar Info File                      | Publish organization and asset information in a `stellar.toml` file linked on-chain          | Anchors, issuers, validators     |
| SEP-5  | Key Derivation Methods                 | Standard HD key derivation for Stellar accounts from BIP-39 mnemonics                        | Wallets, applications            |
| SEP-6  | Deposit and Withdrawal API             | Programmatic anchor-wallet integration; client collects KYC via SEP-12                       | Anchors, wallets, applications   |
| SEP-7  | URI Scheme for Delegated Signing       | Deeplink and QR-code-based payment requests; user signs in their own wallet                  | Wallets, applications            |
| SEP-10 | Stellar Authentication                 | Standard authenticated web sessions for users holding a Stellar account                      | Wallets, exchanges, anchors      |
| SEP-12 | KYC API                                | Standard format for transmitting KYC data between wallets and anchors                        | Anchors, wallets, applications   |
| SEP-20 | Self-Verification of Validator Nodes   | Validators link their account to a domain hosting a `stellar.toml` for node discovery        | Validators                       |
| SEP-24 | Hosted Deposit and Withdrawal          | Interactive anchor-wallet integration; anchor opens a webview and collects KYC directly      | Anchors, wallets, applications   |
| SEP-29 | Account Memo Requirements              | Anchors can declare that incoming payments must include a memo field for compliance           | Anchors, wallets                 |
| SEP-30 | Account Recovery                       | Multi-party account recovery without third-party key control                                 | Wallets, applications            |
| SEP-31 | Cross-Border Payments API              | Direct protocol between two anchors for institution-to-institution cross-border payments     | Anchors                          |
| SEP-38 | Anchor RFQ API                         | Request-for-quote mechanism for anchors to provide exchange rates before transaction execution | Anchors, wallets               |
 
### SEP-6 vs SEP-24 - Choosing the Integration Model
 
Both SEP-6 and SEP-24 solve the same problem (anchor-wallet deposit and withdrawal) but with different UX trade-offs:
 
| Aspect                | SEP-6 (Programmatic)                           | SEP-24 (Hosted)                              |
|-----------------------|------------------------------------------------|----------------------------------------------|
| KYC responsibility    | Client (wallet) collects and sends KYC         | Anchor collects KYC in its own webview       |
| User experience       | User stays entirely within the wallet          | User is sent to a third-party webview        |
| Client complexity     | Higher (must implement SEP-12 KYC collection) | Lower (no KYC collection required)           |
| Anchor complexity     | Lower (no UI to maintain)                     | Higher (must build and maintain a webview UI)|
 
> Source: [Stellar Ecosystem Proposals - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/stellar-ecosystem-proposals)
 
 
## 7. How It All Fits Together
 
The native services of Stellar are designed to work as a unified system for cross-border payments. A complete remittance flow using all five services looks like this:
 
```
1. User deposits USD with a US Anchor (SEP-24 or SEP-6 flow)
2. US Anchor issues tokenized USD to the user's Stellar account
3. User submits a PathPaymentStrictReceive transaction:
     - Source asset: tokenized USD
     - Destination asset: tokenized MXN (issued by a Mexican Anchor)
     - Stellar Core finds the best route via SDEX + Liquidity Pools
     - All conversions happen atomically in a single transaction
4. Mexican Anchor receives the tokenized MXN payment (SEP-31 if B2B)
5. Mexican Anchor pays the recipient in fiat MXN via local bank rails
```
 
Total time: 3-5 seconds on-chain. Total on-chain fee: fraction of a cent.
 
 
## 8. Official Resources
 
| Resource                                     | URL                                                                                              |
|----------------------------------------------|--------------------------------------------------------------------------------------------------|
| SDEX and Liquidity Pools                     | <https://developers.stellar.org/docs/learn/fundamentals/liquidity-on-stellar-sdex-liquidity-pools> |
| Path Payments Guide                          | <https://developers.stellar.org/docs/build/guides/transactions/path-payments>                    |
| Anchors Overview                             | <https://developers.stellar.org/docs/learn/fundamentals/anchors>                                 |
| Anchor Directory                             | <https://anchors.stellar.org>                                                                    |
| Anchor Platform (SDF)                        | <https://developers.stellar.org/docs/platforms/anchor-platform>                                  |
| Stellar Ecosystem Proposals Overview         | <https://developers.stellar.org/docs/learn/fundamentals/stellar-ecosystem-proposals>             |
| SEP GitHub Repository                        | <https://github.com/stellar/stellar-protocol/tree/master/ecosystem>                              |
| SEP-6 (Deposit and Withdrawal API)           | <https://github.com/stellar/stellar-protocol/blob/master/ecosystem/sep-0006.md>                  |
| SEP-10 (Stellar Authentication)              | <https://github.com/stellar/stellar-protocol/blob/master/ecosystem/sep-0010.md>                  |
| SEP-12 (KYC API)                             | <https://github.com/stellar/stellar-protocol/blob/master/ecosystem/sep-0012.md>                  |
| SEP-24 (Hosted Deposit and Withdrawal)       | <https://github.com/stellar/stellar-protocol/blob/master/ecosystem/sep-0024.md>                  |
| SEP-31 (Cross-Border Payments API)           | <https://github.com/stellar/stellar-protocol/blob/master/ecosystem/sep-0031.md>                  |
 
---
 
*Module 05 of 07 - Stellar Knowledge Base*
*Maintained by: [Miguel Fagundez](https://github.com/miguelfagundez) & [Nemorix Group, LLC](https://github.com/nemorixgroup)*
