# Module 04 - Accounts and Transactions
 
> A deep technical study of Stellar accounts, keypairs, trustlines, the sequence number mechanism, operations and transactions, preconditions, multi-sig, and the complete transaction lifecycle from creation to ledger application.
 
 
## Table of Contents
 
1. [Accounts - The Central Data Structure](#1-accounts---the-central-data-structure)
2. [Keypairs and Account IDs](#2-keypairs-and-account-ids)
3. [Trustlines](#3-trustlines)
4. [Sequence Number - The Anti-Replay Mechanism](#4-sequence-number---the-anti-replay-mechanism)
5. [Operations](#5-operations)
6. [Transactions](#6-transactions)
7. [Memo and Muxed Accounts](#7-memo-and-muxed-accounts)
8. [Preconditions](#8-preconditions)
9. [Signatures and Multi-sig](#9-signatures-and-multi-sig)
10. [Transaction Lifecycle](#10-transaction-lifecycle)
11. [Key Operations Reference](#11-key-operations-reference)
12. [Official Resources](#12-official-resources)

 
## 1. Accounts - The Central Data Structure
 
Accounts are the central data structure in Stellar. They hold balances, sign transactions, and issue assets. An account cannot exist without a valid keypair and the required minimum balance of XLM.
 
There are two types of accounts on Stellar:
 
| Type | Address prefix | Description |
|------|----------------|-------------|
| Stellar account | `G...` | Standard user or application account |
| Contract account | `C...` | Soroban smart contract account |
 
This module focuses on `G...` accounts.
 
A Stellar account is composed of the following fields:
 
| Field | Description |
|-------|-------------|
| Account ID | The public key encoded in StrKey format, starting with `G` |
| Balances | XLM balance plus a trustline entry for each held asset |
| Flags | Authorization controls for asset issuance |
| Home domain | Up to 32 characters linking the account to a domain (used with SEP-1) |
| Liabilities | Buying and selling obligations from active SDEX offers |
| Sequence number | Transaction replay protection counter |
| Signers | The set of public keys authorized to sign transactions for this account |
| Thresholds | The weight requirements for low, medium, and high operations |
| Subentries | Count of additional ledger entries owned by the account |
| Sponsored reserves | Tracking of entries whose reserves are paid by another account |
 
> Source: [Accounts - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/stellar-data-structures/accounts)
 
 
## 2. Keypairs and Account IDs
 
Stellar uses the **Ed25519** elliptic curve signature scheme for keypairs. The relationship between keys is:
 
```
Secret Key (32 bytes, Ed25519 seed)
    -> Public Key (32 bytes, Ed25519 public key)
        -> Account ID (StrKey encoded, starts with "G")
```
 
The Account ID is simply the human-readable encoding of the public key. No registration is required to generate a keypair - any valid Ed25519 keypair can become a Stellar account once it receives the minimum XLM balance on the ledger.
 
The StrKey encoding uses a base32 alphabet with a checksum, making addresses resistant to transcription errors. The leading character encodes the key type: `G` for account IDs, `S` for secret keys.
 
> Source: [Signatures and Multisig - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/transactions/signatures-multisig)
 
 
## 3. Trustlines
 
A trustline is an explicit opt-in for an account to hold a specific asset issued by a specific account. Without a trustline, an account cannot receive that asset.
 
The only exception is XLM: as the native token, it requires no trustline. Every account implicitly holds an XLM balance.
 
A trustline is created using the `change_trust` operation, which specifies:
- The asset to trust (code + issuer account)
- An optional limit (maximum amount the account is willing to hold)
### Trustlines and liabilities
 
A trustline tracks two types of liabilities associated with active SDEX offers:
 
| Liability type | Definition |
|----------------|-----------|
| Buying liabilities | Total amount of the asset offered to buy across all active offers owned by the account |
| Selling liabilities | Total amount of the asset offered to sell across all active offers owned by the account |
 
A trustline must always have a balance large enough to cover its selling liabilities, and enough headroom below its limit to accommodate its buying liabilities. Any operation that would violate either constraint fails.
 
### Sending assets to accounts without trustlines
 
It is not possible to send an asset directly to an account that does not have a trustline for that asset. The recommended workaround is to use a **Claimable Balance**: the sender creates a claimable balance with the asset, and the recipient creates the trustline before claiming it.
 
> Source: [Accounts - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/stellar-data-structures/accounts)
 
 
## 4. Sequence Number - The Anti-Replay Mechanism
 
Every Stellar account has a **sequence number** that starts at 0 when the account is created and can only ever increase. Every transaction submitted from that account must carry a sequence number exactly one greater than the account's current sequence number.
 
This mechanism provides two guarantees:
 
**Replay protection.** A signed transaction can only be executed once. If someone captures a valid signed transaction and tries to resubmit it, the sequence number will already have been consumed and the transaction will fail.
 
**Transaction ordering.** Because the sequence number must be strictly sequential, only one transaction per account can be confirmed per ledger close. There is no ambiguity about the order of transactions from a single account.
 
### What happens when a transaction is rejected
 
If a transaction fails basic validity checks and is rejected immediately by Stellar Core, the sequence number is NOT incremented and no fee is charged to the source account. The sequence number is only consumed when a transaction is included in a ledger, even if that transaction ultimately fails during the application phase.
 
> Source: [Operations and Transactions - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/transactions/operations-and-transactions)
 
 
## 5. Operations
 
An operation is an individual command that modifies the ledger. It is the minimum unit of action on Stellar. Operations are used to send payments, create accounts, establish trustlines, place orders on the SDEX, configure account settings, and invoke smart contracts.
 
Every operation falls into one of three threshold categories that determine the signature weight required to authorize it:
 
| Threshold | Typical operations |
|-----------|--------------------|
| Low       | `allow_trust`, `end_sponsoring_future_reserves`, `claim_claimable_balance` |
| Medium    | `payment`, `create_account`, `change_trust`, `manage_sell_offer`, `manage_data`, `path_payment` |
| High      | `set_options`, `account_merge` |
 
Operations are executed against the source account of the transaction unless an operation-level source account override is defined.
 
> Source: [Operations and Transactions - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/transactions/operations-and-transactions)
 
 
## 6. Transactions
 
A transaction is a container that bundles between 1 and 100 operations (classic transactions only; smart contract transactions allow exactly 1 operation). The operations in a transaction are **atomic**: if any single operation fails, the entire transaction fails and no changes are applied to the ledger.
 
A transaction plus its signatures is called a **Transaction Envelope**.
 
### Transaction fields
 
| Field | Required | Description |
|-------|----------|-------------|
| Source account | Yes | The account originating the transaction and paying the fee |
| Sequence number | Yes | Must be exactly `source_account.sequence + 1` |
| Fee | Yes | Maximum inclusion fee the submitter is willing to pay (in stroops) |
| List of operations | Yes | 1 to 100 operations (1 for smart contract transactions) |
| List of signatures | Yes | All required signatures for the operations |
| Memo | No | Optional unstructured data field, up to 28 bytes as text |
| Preconditions | No | Optional validity constraints (time bounds, ledger bounds, etc.) |
 
### Atomicity
 
Transactions are all-or-nothing. If operation 3 of 5 fails during application, operations 1 and 2 are rolled back and the ledger is left unchanged. This guarantee is fundamental to building reliable multi-step payment flows on Stellar.
 
> Source: [Operations and Transactions - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/transactions/operations-and-transactions)
 
 
## 7. Memo and Muxed Accounts
 
### Memo
 
Memos are an optional unstructured data field that can embed identifying information about a transaction. They are commonly used for compliance and routing in payment applications.
 
Supported memo types:
 
| Type | Description |
|------|-------------|
| `MEMO_TEXT` | ASCII or UTF-8 string, up to 28 bytes |
| `MEMO_ID` | 64-bit unsigned integer |
| `MEMO_HASH` | 32-byte hash |
| `MEMO_RETURN` | 32-byte hash representing the transaction being refunded |
 
Common use cases for memos include refund references, invoice IDs, and internal routing identifiers. SEP-29 defines a standard for anchors to declare that incoming payments must include a specific memo field.
 
### Muxed Accounts
 
Muxed accounts (SEP-23) are an alternative to memos for differentiating users within a shared pooled account. A muxed account embeds a 64-bit user ID directly into the account address, creating a virtual sub-account. This approach is cleaner than relying on memo conventions and supports standard address formats across wallets and exchanges.
 
> Source: [Operations and Transactions - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/transactions/operations-and-transactions)
 
 
## 8. Preconditions
 
Preconditions are optional validity constraints that a transaction must satisfy before it can be applied to the ledger. They are checked before any operations execute.
 
| Precondition | Description |
|--------------|-------------|
| **Time bounds** | UNIX timestamp range within which the transaction is valid. Highly recommended to prevent unexecuted transactions from remaining valid indefinitely. If `maxTime` is 0, there is no upper bound and the transaction stays in memory indefinitely. |
| **Ledger bounds** | Valid only within a specific range of ledger numbers. The lower bound is inclusive; the upper bound is exclusive. |
| **Minimum sequence number** | The transaction is only valid when the source account's sequence number `S` satisfies `minSeqNum <= S < tx.seqNum`. Overrides the default strict `sequence + 1` requirement. |
| **Minimum sequence age** | The transaction is valid only after a specified duration (in seconds) has elapsed since the account's sequence number was last updated. |
| **Minimum sequence ledger gap** | Similar to minimum sequence age, but expressed as a number of ledgers rather than seconds. |
| **Extra signers** | Up to two additional public keys whose signatures are required, even if not otherwise needed to authorize the transaction's operations. |
 
Setting time bounds on all transactions is strongly recommended. Without them, a transaction that misses its target ledger will keep retrying indefinitely.
 
> Source: [Operations and Transactions - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/transactions/operations-and-transactions)
 
 
## 9. Signatures and Multi-sig
 
### Basic signing
 
Stellar uses the **Ed25519** signature scheme. Signing a transaction means applying the secret key to the hash of the transaction contents plus the network passphrase. Including the network passphrase in the signed data prevents a valid signature on one network (e.g., Testnet) from being replayed on another (e.g., Mainnet).
 
A transaction with an attached signature is considered authorized by the corresponding public key.
 
### Thresholds and weights
 
Each operation has a threshold category (low, medium, or high). Each signer has an integer weight between 0 and 255. An operation is authorized when the sum of the weights of all valid signatures in the transaction meets or exceeds the threshold for that operation.
 
Default values when an account is created:
- Master key weight: 1
- Low threshold: 0
- Medium threshold: 0
- High threshold: 0
With default settings, the master key alone can authorize any operation. The `set_options` operation changes weights and thresholds to implement custom authorization policies.
 
### Multi-sig examples
 
**Joint account (any one of three can authorize payments):**
```
Master Key Weight: 1
Signer B Weight:   1
Signer C Weight:   1
Low threshold:   0
Medium threshold: 0  (any single signature of weight >= 1 satisfies this)
High threshold:  3  (all three required to change signers)
```
 
**3-of-6 company account:**
```
Master Key Weight: 0  (disabled - account requires employee signatures)
Employees 1-6 Weight: 1 each
Low/Medium/High threshold: 3  (any 3 of 6 employees authorize any operation)
```
 
**Warning:** setting the master key weight to 0 permanently disables it. If no other valid signers exist on the account at that point, the account becomes permanently inaccessible.
 
### Multi-sig constraints
 
- Maximum 20 signatures per transaction
- Each additional signer beyond the master key consumes 1 base reserve (0.5 XLM)
- Providing more signatures than the threshold requires causes the transaction to fail with `TX_BAD_AUTH_EXTRA`
### Alternate signature types
 
| Type | How it works | Use case |
|------|-------------|----------|
| Pre-authorized Transaction | The hash of a future transaction is added as a signer; it is auto-removed when that transaction executes | Escrow, pre-committed transactions |
| Hash(x) | Anyone who knows the secret value `x` can authorize; `x` becomes public once used | Atomic cross-chain swaps, HTLCs |
 
> Source: [Signatures and Multisig - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/transactions/signatures-multisig)
 
 
## 10. Transaction Lifecycle
 
Every transaction follows an 11-step lifecycle from creation to final ledger application:
 
| Step | Actor | Description |
|------|-------|-------------|
| 1. Creation | Transaction creator | Build the transaction: source account, sequence number, operations, fee, optional memo and preconditions |
| 1b. Simulation | Developer (Soroban only) | Smart contract transactions are simulated via `simulateTransaction` RPC to detect errors and estimate resource usage |
| 2. Signing | Transaction signers | Collect all required signatures; the result is the Transaction Envelope |
| 3. Submitting | Transaction submitter | Submit via Stellar RPC or directly to a Stellar Core node. If invalid, rejected immediately; sequence number is NOT incremented; no fee charged |
| 4. Propagating | Validator node | The receiving node floods the valid transaction to all connected peers across the entire network |
| 5. Candidate set | Validator | At ledger close time, each validator collects all known valid transactions into a candidate transaction set. If operations exceed the ledger limit, higher-fee transactions are prioritized |
| 6. Nomination | Validator | The candidate set is nominated to the SCP consensus process |
| 7. SCP final set | Validator network | SCP resolves differences between candidate sets and determines a single transaction set, close time, and any protocol upgrades |
| 8. Apply order | Validator network | The transaction set order is shuffled (randomized) to prevent front-running by competing transactions |
| 9. Fee collection | Validator | Fees are collected from all transactions simultaneously before application |
| 10. Application | Validator | Each transaction is applied in order: sequence number incremented, validity rechecked, operations applied one by one. If any operation fails, the entire transaction is rolled back |
| 11. Protocol upgrades | Validator | Any pending protocol upgrades are applied. The next ledger cycle begins |
 
### Key behaviors
 
**Rejected at step 3 (immediate rejection):** sequence number unchanged, no fee charged.
 
**Failed at step 10 (failed during application):** sequence number IS incremented, fee IS charged. The transaction made it into the ledger but failed execution (for example, due to insufficient balance detected only at apply time).
 
**Missed the transaction set:** the transaction stays in memory and retries for subsequent ledgers. After a certain number of attempts, it is banned for additional ledgers. Setting time bounds prevents this indefinite queuing.
 
> Source: [Transaction Lifecycle - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/transactions/transaction-lifecycle)
 
 
## 11. Key Operations Reference
 
| Operation | Threshold | Description |
|-----------|-----------|-------------|
| `create_account` | Medium | Create a new account with an initial XLM balance |
| `payment` | Medium | Send an asset (XLM or any issued asset) to another account |
| `path_payment_strict_send` | Medium | Multi-hop payment fixing the amount sent |
| `path_payment_strict_receive` | Medium | Multi-hop payment fixing the amount received |
| `manage_sell_offer` | Medium | Create, modify, or cancel a sell order on the SDEX |
| `manage_buy_offer` | Medium | Create, modify, or cancel a buy order on the SDEX |
| `create_passive_sell_offer` | Medium | Create a sell order that does not cross same-price counter-orders |
| `change_trust` | Medium | Create, modify, or delete a trustline |
| `allow_trust` | Low | Authorize or revoke an account's ability to hold your issued asset |
| `set_options` | High | Configure signers, thresholds, flags, and home domain |
| `account_merge` | High | Merge one account into another, transferring all XLM |
| `manage_data` | Medium | Write or delete a key-value data entry on the account |
| `bump_sequence` | Medium | Manually advance an account's sequence number |
| `begin_sponsoring_future_reserves` | Medium | Start a sponsored reserves session |
| `end_sponsoring_future_reserves` | Low | Close a sponsored reserves session |
| `revoke_sponsorship` | Medium | Transfer or cancel sponsorship of a ledger entry |
| `clawback` | Medium | Reclaim an issued asset from a holder's account (if enabled) |
| `invoke_host_function` | Medium | Deploy or invoke a Soroban smart contract |
| `liquidity_pool_deposit` | Medium | Deposit assets into a liquidity pool |
| `liquidity_pool_withdraw` | Medium | Withdraw assets from a liquidity pool |
 
> Source: [List of Operations - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/transactions/list-of-operations)
 
 
## 12. Official Resources
 
| Resource | URL |
|----------|-----|
| Accounts (Data Structures) | <https://developers.stellar.org/docs/learn/fundamentals/stellar-data-structures/accounts> |
| Operations and Transactions | <https://developers.stellar.org/docs/learn/fundamentals/transactions/operations-and-transactions> |
| List of Operations | <https://developers.stellar.org/docs/learn/fundamentals/transactions/list-of-operations> |
| Signatures and Multisig | <https://developers.stellar.org/docs/learn/fundamentals/transactions/signatures-multisig> |
| Transaction Lifecycle | <https://developers.stellar.org/docs/learn/fundamentals/transactions/transaction-lifecycle> |
| Claimable Balances Guide | <https://developers.stellar.org/docs/build/guides/transactions/claimable-balances> |
| Pooled Accounts, Muxed Accounts and Memos | <https://developers.stellar.org/docs/build/guides/transactions/pooled-accounts-muxed-accounts-memos> |
| Fee-Bump Transactions | <https://developers.stellar.org/docs/build/guides/transactions/fee-bump-transactions> |
 
---
 
*Module 04 of 07 - Stellar Knowledge Base*  
*Maintained by: [Miguel Fagundez](https://github.com/miguelfagundez) & [Nemorix Group, LLC](https://github.com/nemorixgroup)*
