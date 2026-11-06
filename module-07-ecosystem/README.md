# Module 07 - Ecosystem and Future

> A study of the Stellar ecosystem as of November 2026: USDC and stablecoins, remittance and payout infrastructure, institutional tokenization, recent protocol upgrades, the SDF product roadmap, independent research from the Bank for International Settlements (BIS), and design considerations for US to Latin America remittance platforms.

---

## Table of Contents

1. [Stellar as a Payments Rail](#1-stellar-as-a-payments-rail)
2. [USDC and Stablecoins on Stellar](#2-usdc-and-stablecoins-on-stellar)
3. [Remittance and Payout Infrastructure](#3-remittance-and-payout-infrastructure)
4. [Tokenization and Institutional Adoption](#4-tokenization-and-institutional-adoption)
5. [Protocol Evolution 2025 to 2026](#5-protocol-evolution-2025-to-2026)
6. [SDF Product Roadmap (2025 Plan)](#6-sdf-product-roadmap-2025-plan)
7. [What Independent Research Says (BIS)](#7-what-independent-research-says-bis)
8. [Application: US to LATAM Remittance Platforms](#8-application-us-to-latam-remittance-platforms)
9. [Scope and Verification Notes](#9-scope-and-verification-notes)
10. [Official Resources](#10-official-resources)

---

## 1. Stellar as a Payments Rail

The Stellar Development Foundation (SDF) publishes the following headline figures for remittance providers. They come from the SDF itself, so they should be read as vendor-reported numbers.

| Metric | Value reported by SDF |
|--------|-----------------------|
| Time to finality | About 5 seconds |
| Countries with USDC cash-out (via MoneyGram Ramps) | 170+ |
| Cash-out locations | 475,000+ |
| Network uptime | 99.998% since 2014 |

The SDF positions the main operational benefit as reduced prefunding: because payments are final in seconds, remittance providers hold less cash abroad and can add corridors without adding balances in each destination.

> Source: [Stellar for Remittances - stellar.org](https://stellar.org/solutions/remittances)

---

## 2. USDC and Stablecoins on Stellar

### USDC timeline

| Date | Event |
|------|-------|
| October 2020 | Centre Consortium (co-founded by Circle and Coinbase) announces Stellar as an official chain for USDC |
| February 2, 2021 | Circle and the SDF announce USDC is live on Stellar |

At launch, Stellar was the third blockchain with native USDC support. Businesses and developers could access Stellar USDC through a single Circle Account and its API services.

### How USDC exists on Stellar

USDC is a native Stellar asset, identified by its asset code plus its issuer account (see Module 04). Two consequences follow:

- Any account that wants to hold USDC must first establish a trustline to the issuer.
- Only the issuing account can create more of the asset, which is why Circle designed a dedicated minting process for Stellar.

### Stablecoin activity

Research by Allium, published by the SDF in March 2026 with data through February 2026, reports the following for the network:

| Metric | Value |
|--------|-------|
| Stablecoins supported | 17 |
| Fiat currencies covered | 9+ |
| Transactions per month | About 95.9 million |
| Monthly settlement volume | $2B+ |

The SDF also reports that PYUSD is live on Stellar with protocol-native clawback controls.

### Cross-chain transfers

The official Tokens documentation includes a section on cross-chain transfers using Circle's CCTP. Review that page directly before designing any cross-chain flow, since supported chains and requirements change.

> Sources: [Centre announcement - stellar.org](https://stellar.org/press/centre-consortium-announces-stellar-as-an-official-chain-for-usdc) | [USDC is live on Stellar - stellar.org](https://stellar.org/press/usdc-is-live-on-the-stellar-network) | [How we mint USDC on Stellar - circle.com](https://www.circle.com/blog/how-we-mint-stellar-usdc) | [Stellar for Remittances - stellar.org](https://stellar.org/solutions/remittances)

---

## 3. Remittance and Payout Infrastructure

### 3.1 MoneyGram Ramps

MoneyGram has worked with Stellar since 2021. MoneyGram Ramps provides cash-to-crypto and crypto-to-cash conversion at participating MoneyGram locations. Wallets integrate through SEP-24 (see Module 05), which standardizes the KYC flow across wallet providers.

| Fact | Detail |
|------|--------|
| Cash-out presence | 170+ countries |
| Cumulative volume at the three-year mark | Nearly $30 million |
| Top countries by cash-in and cash-out | United States, Canada, Poland, Ukraine, Colombia, Mexico, Uganda, United Kingdom, Ecuador |

The SDF describes an example in which a worker in Mexico received a salary in USDC through the Stellar Disbursement Platform via the Decaf wallet, then cashed out locally with MoneyGram or sent funds to family in Venezuela.

> Sources: [Three years of impact - stellar.org](https://stellar.org/blog/foundation-news/three-years-with-moneygram) | [MoneyGram case study - stellar.org](https://stellar.org/case-studies/moneygram-international)

### 3.2 Airtm

The SDF reports that Airtm pays more than 250,000 recipients in 150+ countries on Stellar, using a single transaction to convert currency and deliver the payout (the Path Payments mechanism from Module 05).

> Source: [Stellar for Remittances - stellar.org](https://stellar.org/solutions/remittances)

### 3.3 Stellar Disbursement Platform (SDP)

The SDP is an open-source backend and frontend for bulk payouts such as payroll, cash assistance, gig work, vendor payments, and government payments.

| Feature | Detail |
|---------|--------|
| Batch size | Up to 10,000 payments in a single batch |
| Recipient onboarding | SMS or email invitation; wallets created on demand, existing wallets supported |
| Recipient verification | OTP code plus one of: date of birth, personal PIN, or national ID |
| Privacy | Recipients' personal data stays off chain; the wallet does not process or store verification data |
| User roles | Four levels: Owner, Financial Controller, Viewer, Developer |
| Funding | The sender funds a distribution account with a Stellar asset such as USDC |
| Compatible wallets (examples listed by SDF) | Decaf, Beans App, Vibrant Assist, Freedom Pay, Vesseo, Digibank, VIA Wallet |

Disbursement flow, as documented by the SDF:

1. The organization funds the distribution account with a Stellar asset.
2. An administrator uploads a CSV with payment details.
3. First-time recipients receive an SMS invitation to install a Stellar-enabled wallet.
4. Recipients who already have a registered wallet are paid immediately.
5. New recipients sign up, verify their identity through the SDP web view, and are paid once the information matches the CSV.

### 3.4 Humanitarian payouts

The SDF reports that UNHCR delivered $4.6 million to 2,500 households in Ukraine through Stellar Aid Assist. UNHCR and the International Rescue Committee use Stellar to send aid as USDC directly to recipients' wallets.

> Sources: [Stellar Disbursement Platform - stellar.org](https://stellar.org/sdp) | [SDP Overview - developers.stellar.org](https://developers.stellar.org/docs/stellar-disbursement-platform/overview) | [SDP wallets and use cases - stellar.org](https://stellar.org/products-and-tools/disbursements)

---

## 4. Tokenization and Institutional Adoption

### 4.1 Franklin Templeton (BENJI)

In April 2021, Franklin Templeton launched the Franklin OnChain U.S. Government Money Fund (FOBXX, token ticker BENJI) on Stellar. It was the first U.S.-registered money market fund to use a public blockchain as the official system of record for transactions and share ownership.

Why this matters technically: the fund needed to know every holder, authorize every transfer, and be able to freeze accounts and claw back shares under a court order. On Stellar these are protocol-level operations, so no custom smart contract sits between the fund and its regulatory obligations.

| Control | How it works on Stellar |
|---------|-------------------------|
| Holder authorization | The issuer authorizes a wallet after KYC; unauthorized wallets cannot receive the token |
| Freeze | The issuer can freeze an account |
| Clawback | The issuer can reclaim shares; it settles like any other Stellar transaction |

Reported figures: the SDF case study cites $605M of BENJI on Stellar as of October 6, 2026 (per rwa.xyz) and $654M of AUM on Stellar in its results section. Treat both as approximate and check the live source.

### 4.2 Three tokenization models

| Model | Description |
|-------|-------------|
| Stellar assets | Issued at the protocol layer with built-in controls (authorization flags, freeze, clawback). Each has a paired Stellar Asset Contract implementing the SEP-41 token interface |
| Contract tokens | Custom WASM smart contracts for issuers that need full programmability |
| SEP-57 / ERC-3643 tokens | Contract-token standards for regulated real-world assets needing on-chain identity and compliance rules |

### 4.3 DTCC

DTCC and the SDF announced plans to enable tokenization of DTC-custodied assets on Stellar, building on a December 2025 SEC No-Action Letter. DTC-tokenized assets are expected to become available on Stellar in the first half of 2027. This is a stated plan, not a live service. Asset classes under evaluation include constituents of the Russell 1000, ETFs tracking major indices, and U.S. Treasury securities.

> Sources: [Franklin Templeton case study - stellar.org](https://stellar.org/case-studies/franklin-templeton) | [DTCC case study - stellar.org](https://stellar.org/case-studies/dtcc)

---

## 5. Protocol Evolution 2025 to 2026

Stellar protocol upgrades are network-wide changes that validators adopt by vote.

| Protocol | Mainnet date | Summary |
|----------|--------------|---------|
| 23 "Whisk" | September 3, 2025 | Unified event streams across classic operations and Soroban contracts; parallel smart contract transaction processing; new transaction meta structure (TransactionMetaV4) with breaking changes for software that reads raw meta |
| 24 | October 22, 2025 | Stability upgrade that fixed a bug in the state archival feature; no XDR changes |
| 25 "X-Ray" | January 22, 2026 | New cryptographic primitives for smart contracts: BN254 elliptic curve operations (CAP-74) and Poseidon/Poseidon2 permutations (CAP-75) |
| 26 | May 6, 2026 | Live on Mainnet with Stellar Core 26.1.0 |

Notes:

- Soroban RPC was renamed Stellar RPC; from Protocol 23, soroban-rpc images and packages are no longer published.
- The Go SDK packages (horizonclient, rpcclient, txnbuild) moved to a centralized repository, `github.com/stellar/go-stellar-sdk`, with a new module name.
- This module does not describe the contents of Protocol 26. See the release notes linked below.

> Sources: [Software Versions - developers.stellar.org](https://developers.stellar.org/docs/networks/software-versions) | [Protocol Upgrades - stellar.org](https://stellar.org/protocol-upgrades) | [Protocol 23 guide - stellar.org](https://stellar.org/blog/protocol-23-upgrade-guide) | [Protocol 24 guide - stellar.org](https://www.stellar.org/blog/developers/protocol-24-upgrade-guide) | [Protocol 25 guide - stellar.org](https://stellar.org/blog/developers/stellar-x-ray-protocol-25-upgrade-guide)

---

## 6. SDF Product Roadmap (2025 Plan)

The roadmap page available at the time of writing is titled "2025 SDF Product Roadmap." It frames the network as feature complete and the 2025 focus as scalability and usability. The items below are plans published by the SDF; their delivery status was not independently verified for this module.

### Scalability targets

| Initiative | Stated goal |
|------------|-------------|
| Faster ledgers | Ledger close time of 2.5 seconds, down from 5 |
| Throughput | Theoretical peak of 5,000 TPS through concurrency, caching, and ahead-of-time compilation of Soroban contracts |
| Tier 1 resilience | Grow from 7 to 13 Tier 1 organizations by the end of 2025 |
| Payout capacity | 100x more transactions per click using Merkle trees |
| Token standards | OpenZeppelin standards for fungible, non-fungible, multi, security, and real-world asset tokens |

### Usability targets

| Initiative | Stated goal |
|------------|-------------|
| SDP single-use wallets | On-demand wallets so recipients need no blockchain experience |
| SDP payment flows | Support for more enterprise payment flows, including crypto and fiat |
| Freighter advanced auth | Passkeys and social sign-in recovery using Soroban smart wallets |
| Stellar Lab 4.0 | Transaction simulation, debugging, and resource profiling |

> Source: [SDF Product Roadmap - stellar.org](https://stellar.org/foundation/roadmap)

---

## 7. What Independent Research Says (BIS)

The BIS publishes research on cross-border payments and crypto assets. Its working papers state the views of their authors, not necessarily those of the BIS.

### 7.1 BIS Working Paper 1265 (May 2025)

*DeFiying gravity? An empirical analysis of cross-border Bitcoin, Ether and stablecoin flows*, by Auer, Lewrick, and Paulick.

| Finding | Summary |
|---------|---------|
| Scale | Flows across 184 countries from 2017 to mid-2024 peaked at around USD 2.6 trillion in 2021, with stablecoins close to half of the volume |
| Native crypto assets | Driven mainly by speculative motives and global funding conditions |
| Stablecoins | Transactional motives play a significant role, with a strong association with higher costs of traditional remittances |
| Frictions | Geographic distance matters much less than for traditional financial flows |
| Policy | Capital flow management measures appear ineffective, and may correlate with increased flows |

### 7.2 BIS Working Paper 1385 (October 19, 2026)

*Competing rails for cross-border payments: banks, fintechs, and stablecoins*, by Du, Huang, and Scharfstein.

The paper compares the cost of sending cross-border payments over three rails: traditional banks using correspondent banking and SWIFT, fintech money transfer operators, and stablecoin-based transfers.

| Finding | Summary |
|---------|---------|
| Fintechs | Fintech money transfer operators have significantly compressed the cost of cross-border payments |
| Stablecoins | They are not a panacea for cross-border payment frictions |
| Competitiveness | Depends not only on blockchain technology but also on market liquidity, reliable access to fiat currencies, regulatory compliance, and trust in intermediaries |

### 7.3 The "stablecoin sandwich"

In the authors' conference presentation (25th BIS Annual Conference, June 26, 2026), a stablecoin transfer is described as three layers: an on-ramp from fiat to stablecoin, an on-chain rail, and an off-ramp back to fiat. Costs accumulate at each layer. The same presentation notes that remittances to developing countries exceed USD 700 billion per year.

**Takeaway for builders:** the blockchain leg is typically the cheapest and fastest part of the journey. Cost, speed, and risk are decided by ramp coverage, FX, compliance, and user experience.

> Sources: [BIS Working Paper 1265](https://www.bis.org/publ/work1265.htm) | [BIS Working Paper 1385](https://www.bis.org/publications/working-paper-1385-competing-rails-crossborder-payments-banks-fintechs-and-stablecoins) | [Du, Huang, Scharfstein conference slides](https://www.bis.org/events/20260626/25th-bis-annual-conference/25th-annual-conference-du.pdf)

---

## 8. Application: US to LATAM Remittance Platforms

This section connects the verified material in Modules 01 to 07 to the design of a US to Latin America remittance platform. It is an analytical summary based on the official sources above, not a description of any specific product's architecture.

| Concern | Relevant Stellar mechanism | Where covered |
|---------|----------------------------|---------------|
| Last-mile cash-out | Anchors, SEP-24, MoneyGram Ramps, the anchor directory | Modules 05, 07 |
| KYC and authentication | SEP-10 (authentication), SEP-12 (KYC API), SEP-24 hosted flow | Module 05 |
| Institution-to-institution payments | SEP-31 cross-border payments API, SEP-38 quotes | Module 05 |
| Compliance memos | Transaction memos, muxed accounts, SEP-29 memo requirements | Modules 04, 05 |
| Cross-currency settlement | Path Payments across the SDEX and liquidity pools | Module 05 |
| Asset-level controls | Trustline authorization, freeze, clawback | Modules 04, 07 |
| Onboarding economics | 1 XLM minimum balance, 0.5 XLM per trustline, sponsored reserves, fee-bump transactions | Module 03 |
| Bulk payouts | Stellar Disbursement Platform | Module 07 |
| Transaction cost | 100 stroops (0.00001 XLM) minimum fee per operation | Module 03 |
| Real total cost to the user | On-ramp, FX, and off-ramp costs dominate (BIS) | Module 07 |

Corridor evidence from official sources: Colombia, Mexico, and Ecuador appear among the top MoneyGram Ramps countries, and the SDF cites a Mexico-to-Venezuela flow in its SDP example.

### Questions to resolve with primary sources

- Which anchors and ramps operate in each target country, and under which licenses (use the SDF anchor directory and each anchor's own stellar.toml, SEP-1).
- Which stablecoin issuers and regulatory frameworks apply to each corridor.
- How the end-to-end price compares with fintech money transfer operators, as the BIS research recommends measuring.

---

## 9. Scope and Verification Notes

- The ecosystem figures in Sections 1 to 4 are reported by the SDF and its partners. They are marketing-adjacent and change often; check the linked pages for current numbers.
- The roadmap in Section 6 is a 2025 plan. Check protocol release notes to confirm which items shipped.
- This module does not include a cross-chain comparison with other networks, because comparisons require equally verified data for each network. See the sibling knowledge bases for those networks.
- Items reported only in trade press, such as MoneyGram's own dollar stablecoin on Stellar (MGUSD) and details of the April 2026 MoneyGram and SDF partnership extension, are not described here. The SDF remittances page mentions MoneyGram's stablecoin as a case study; consult that page for the official account.

---

## 10. Official Resources

| Resource | URL |
|----------|-----|
| Stellar for Remittances | <https://stellar.org/solutions/remittances> |
| Stablecoins on Stellar | <https://stellar.org/solutions/stablecoins> |
| MoneyGram case study | <https://stellar.org/case-studies/moneygram-international> |
| Three years with MoneyGram | <https://stellar.org/blog/foundation-news/three-years-with-moneygram> |
| Franklin Templeton case study | <https://stellar.org/case-studies/franklin-templeton> |
| DTCC case study | <https://stellar.org/case-studies/dtcc> |
| Stellar Disbursement Platform | <https://stellar.org/sdp> |
| SDP documentation overview | <https://developers.stellar.org/docs/stellar-disbursement-platform/overview> |
| USDC is live on Stellar (SDF press) | <https://stellar.org/press/usdc-is-live-on-the-stellar-network> |
| How Circle mints USDC on Stellar | <https://www.circle.com/blog/how-we-mint-stellar-usdc> |
| Software versions and protocol releases | <https://developers.stellar.org/docs/networks/software-versions> |
| Protocol upgrades | <https://stellar.org/protocol-upgrades> |
| SDF product roadmap | <https://stellar.org/foundation/roadmap> |
| Anchor directory | <https://anchors.stellar.org> |
| BIS Working Paper 1265 | <https://www.bis.org/publ/work1265.htm> |
| BIS Working Paper 1385 | <https://www.bis.org/publications/working-paper-1385-competing-rails-crossborder-payments-banks-fintechs-and-stablecoins> |

---

*Module 07 of 07 - Stellar Knowledge Base*
*Last updated: November 2026*
*Maintained by: [Miguel Fagundez](https://github.com/miguelfagundez) & [Nemorix Group, LLC](https://github.com/nemorixgroup)*
