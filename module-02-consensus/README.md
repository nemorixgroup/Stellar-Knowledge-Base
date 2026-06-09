# Module 02 - Consensus Protocol (SCP)
 
> A deep technical study of the Stellar Consensus Protocol (SCP), the Federated Byzantine Agreement (FBA) model, quorum mechanics, federated voting, and how the network reaches finality on every transaction.
 
 
## Table of Contents
 
1. [The Problem SCP Solves](#1-the-problem-scp-solves)
2. [What is Federated Byzantine Agreement (FBA)?](#2-what-is-federated-byzantine-agreement-fba)
3. [SCP Components](#3-scp-components)
4. [Federated Voting - Step by Step](#4-federated-voting---step-by-step)
5. [Consensus Rounds](#5-consensus-rounds)
6. [Safety vs Liveness - The Trade-off](#6-safety-vs-liveness---the-trade-off)
7. [Validators and Tier 1 Organizations](#7-validators-and-tier-1-organizations)
8. [Four Key Properties of SCP](#8-four-key-properties-of-scp)
9. [SCP vs Other Consensus Mechanisms](#9-scp-vs-other-consensus-mechanisms)
10. [Official Resources](#10-official-resources)

 
## 1. The Problem SCP Solves
 
In any decentralized payment system, the fundamental challenge is: **how do thousands of independent computers agree on the exact state of the ledger, without any central authority controlling them?**
 
This is known as the **Byzantine Generals Problem**: imagine several generals who must coordinate an attack, but some may be traitors. How can they reach a reliable agreement if they cannot verify who is lying?
 
Classical solutions each make different trade-offs:
 
| Approach             | Example           | How it works                              | Trade-off                              |
|----------------------|-------------------|-------------------------------------------|----------------------------------------|
| Proof of Work        | Bitcoin           | Most computational power wins the block   | Slow, high energy cost                 |
| Proof of Stake       | Ethereum          | Token stake determines voting power       | Efficient, but favors large holders    |
| Classical PBFT       | Ripple, Hyperledger | Fixed set of known validators vote       | Fast, but requires a closed member list |
| **Federated BFT (FBA)** | **Stellar**    | Each node chooses its own trusted peers   | Open, decentralized, fast              |
 
SCP is a construction for FBA - a fourth category invented specifically for Stellar, introduced by Prof. David Mazières in 2015.
 
> Source: [Stellar Consensus Protocol - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/stellar-consensus-protocol)
 
 
## 2. What is Federated Byzantine Agreement (FBA)?
 
In FBA, **each node independently decides who to trust**. There is no global approved validator list. There is no central authority declaring which nodes are valid.
 
The key insight from the SCP whitepaper:
 
> "FBA achieves robustness through quorum slices - individual trust decisions made by each node that together determine system-level quorums. Slices bind the system together much the way individual networks' peering and transit decisions now unify the Internet."
> - David Mazières, SCP Whitepaper
 
The analogy is precise: the internet has no central authority deciding which networks connect to each other. Each ISP independently chooses its peering relationships. The result is a global network that functions through the sum of local trust decisions. SCP works the same way.
 
In practice, each node configures a **Quorum Set** - the group of nodes it considers trustworthy enough to make decisions alongside it. These local trust decisions, when they overlap sufficiently across the network, produce global consensus.
 
> Source: [SCP Whitepaper - stellar.org/learn/stellar-consensus-protocol](https://stellar.org/learn/stellar-consensus-protocol)
 
 
## 3. SCP Components
 
### 3.1 Quorum Set
 
The set of nodes a given node directly trusts. Each node operator configures this independently. Example:
 
```
Node A trusts: [SDF-1, SDF-2, Franklin Templeton Validator, LOBSTR Validator]
```
 
Validators are typically added to quorum sets because of **real-world trust relationships**: known organizations, verified identities, and demonstrated reliability.
 
### 3.2 Threshold
 
The minimum number of nodes from the Quorum Set that must agree for the node to proceed. Example:
 
```
Node A requires: at least 3 of its 4 trusted nodes to agree
```
 
Thresholds are expressed as integers. A threshold of 3 out of 4 means the node needs any combination of 3 agreeing nodes to move forward.
 
### 3.3 Quorum Slice
 
The specific combination of nodes that satisfies the threshold. For a node with 4 trusted peers and a threshold of 3, valid quorum slices include:
- `[SDF-1, SDF-2, Franklin Templeton]`
- `[SDF-2, LOBSTR, Franklin Templeton]`
- `[SDF-1, LOBSTR, Franklin Templeton]`
A quorum slice is not fixed - any combination that meets the threshold qualifies.
 
### 3.4 Quorum
 
A **quorum** is a set of nodes sufficient to reach agreement, where each node in the set is part of at least one quorum slice.
 
The critical distinction:
- A **Quorum Set** is local - defined by each individual node
- A **Quorum** is emergent - it results from the intersection of quorum sets across the entire network
For the network to function safely, the quorum sets of honest nodes must have sufficient **quorum intersection**: every two quorums must share at least one node in common. If quorums become completely disjoint, the network could split into two groups that confirm contradictory ledgers.
 
### 3.5 Node Blocking Set
 
A **node blocking set** is any subset of nodes in a quorum set that, if they disagree or fail, prevents a node from reaching agreement.
 
For a node requiring 3 of 4 agreements, any group of 2 nodes is a node blocking set - if those 2 disagree, the node cannot reach the required threshold of 3.
 
### 3.6 Statement
 
A **statement** is a formal expression of a node's opinion about which transaction set should be applied to a given ledger. Example:
 
```
"I propose this transaction set for ledger number 800"
```
 
Federated voting is the process by which statements move from individual proposals to network-wide confirmed facts.
 
> Source: [Stellar Consensus Protocol - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/stellar-consensus-protocol)
 
 
## 4. Federated Voting - Step by Step
 
When a node proposes a statement, it passes through three phases before being considered final. At any given moment, a node can hold one of four opinions about a statement:
 
| Opinion   | Meaning                                                                                      |
|-----------|----------------------------------------------------------------------------------------------|
| Unknown   | No opinion yet                                                                               |
| Voted     | The statement is valid, but it is not yet safe to act on it                                  |
| Accepted  | Enough nodes supported the statement, but it is still not safe to act on it                  |
| Confirmed | It is safe to act on the statement; no honest node can confirm a contradicting statement      |
 
### Phase 1 - VOTE
 
A node votes for a statement if it is consistent with its previous votes. The node announces to its quorum set: *"I propose this transaction set for ledger #800."* At this point, it knows the proposal is valid but does not yet know if it is safe to commit.
 
### Phase 2 - ACCEPT
 
A node accepts a statement if either:
- Every node in at least one of its quorum slices has voted for or accepted the statement, **OR**
- Its blocking set has accepted the statement (in this case, even if the node previously voted for a contradicting value, it abandons that vote and accepts the new statement)
The second rule is important: it prevents a node from being permanently stuck by an earlier vote that the rest of the network has moved past.
 
### Phase 3 - CONFIRM
 
A node confirms a statement when every node in at least one of its quorum slices has **accepted** it.
 
Once a statement is confirmed, the node knows it is safe to act. Even if some nodes in its quorum have not yet confirmed it, they will not be able to confirm anything contradicting it. The transaction becomes **final and irreversible**.
 
### Visual summary
 
```
UNKNOWN -> VOTED -> ACCEPTED -> CONFIRMED (final)
                 ^
                 |
          (blocking set
           shortcut)
```
 
> Source: [Stellar Consensus Protocol - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/stellar-consensus-protocol)
 
 
## 5. Consensus Rounds
 
Each consensus cycle runs two sequential protocols:
 
### 5.1 Nomination Protocol
 
Nodes propose **candidate transaction sets** to be included in the next ledger.
 
- Once a node confirms its first candidate, it stops nominating new transaction sets (but continues to accept or confirm previously nominated ones)
- This convergence guarantee ensures that eventually all nodes settle on the same set of candidates
- If all nodes stop introducing new values and continue confirming what others confirmed, they will end up with an identical candidate list
A node may begin the ballot protocol as soon as it confirms a candidate. Nomination continues running in the background.
 
### 5.2 Ballot Protocol
 
The ballot protocol takes the nominated candidates and drives them to final confirmation. It consists of two sub-steps:
 
1. **Prepare** - verifies that the node's quorum slice holds the correct value and is willing to commit to it
2. **Commit** - confirms that the node's quorum slice actually commits to that value
Once the ballot protocol completes, the ledger closes. All honest nodes have applied the same transaction set and hold the same ledger state. This process repeats every **5-7 seconds**.
 
> Source: [Stellar Stack - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/stellar-stack)
 
 
## 6. Safety vs Liveness - The Trade-off
 
Every consensus mechanism must navigate a fundamental trade-off between three properties:
 
| Property        | Meaning                                                                 |
|-----------------|-------------------------------------------------------------------------|
| Fault Tolerance | The system continues operating despite node failures or malfunctions    |
| Safety          | No two nodes ever confirm different values (no ledger fork)             |
| Liveness        | The system can always produce a result without getting stuck            |
 
No consensus mechanism can guarantee all three properties perfectly. SCP makes an explicit choice:
 
**SCP prioritizes Fault Tolerance and Safety over Liveness.**
 
In practice, this means that in extreme scenarios where nodes cannot safely agree, the ledger may pause rather than produce two contradictory versions. For a payment network, this is the correct trade-off: it is far better to halt temporarily than to record conflicting transactions that could result in double-spends or fund loss.
 
> Source: [Stellar Consensus Protocol - developers.stellar.org](https://developers.stellar.org/docs/learn/fundamentals/stellar-consensus-protocol)
 
 
## 7. Validators and Tier 1 Organizations
 
### Who can run a validator?
 
Anyone can run a Stellar Core node. There are no monetary rewards for doing so. Validators contribute to network security and decentralization, which benefits all products and services built on Stellar.
 
There are two types of nodes:
- **Basic Validator**: participates in consensus, does not publish a history archive
- **Full Validator**: participates in consensus and publishes a publicly accessible history archive
### Tier 1 Organizations
 
Tier 1 organizations are the group whose validators bear the primary responsibility for network safety and liveness. Most other nodes on the network include Tier 1 validators in their quorum sets.
 
Requirements to become a Tier 1 organization:
- Run three geographically dispersed Full Validators
- Sustain 99.9%+ uptime (visible on Obsrvr Radar)
- Complete SEP-20 and SEP-1 self-verification
- Actively coordinate with the existing Tier 1 community
Joining Tier 1 is not a unilateral SDF decision. Every existing Tier 1 organization independently decides whether to add a new organization to its quorum set. The process typically takes months.
 
### Quorum set auto-generation
 
Because crafting an optimal quorum set is complex, `stellar-core` automatically generates a quorum set from structured configuration. The auto-generation rules:
 
- Validators sharing the same home domain are grouped together with a simple majority threshold (2f+1)
- Heterogeneous groups are given a threshold assuming Byzantine failure (3f+1)
- Entities are grouped by quality (HIGH, MEDIUM, LOW) and nested accordingly
- HIGH quality entities have decision-making priority
> Source: [Tier 1 Organizations - developers.stellar.org](https://developers.stellar.org/docs/validators/tier-1-orgs)
 
 
## 8. Four Key Properties of SCP
 
From the original SCP whitepaper by David Mazières, SCP is the first provably safe consensus mechanism that simultaneously provides all four of the following:
 
| Property              | Description                                                                           |
|-----------------------|---------------------------------------------------------------------------------------|
| Decentralized Control | No central authority imposes participation or determines whose vote counts            |
| Low Latency           | Consensus reached in 3-5 seconds without mining or staking computation               |
| Flexible Trust        | Each node independently selects its own trusted peers                                 |
| Asymptotic Security   | Security based on digital signatures, not computational power - scales with math      |
 
> Source: [SCP Proof and Code - stellar.org](https://stellar.org/blog/foundation-news/stellar-consensus-protocol-proof-code)
 
 
## 9. SCP vs Other Consensus Mechanisms
 
| Property              | SCP (Stellar)        | RPCA (XRPL)          | Hashgraph (Hedera)  | PoW (Bitcoin)      | PoS (Ethereum)     |
|-----------------------|----------------------|----------------------|---------------------|--------------------|--------------------|
| Consensus type        | Federated BFT        | Federated BFT        | Async BFT           | Nakamoto consensus | BFT + PoS          |
| Validator selection   | Self-selected trust  | UNL (published list) | Governing Council   | Mining competition | Staking            |
| Finality              | 3-5 seconds          | 3-5 seconds          | 3-5 seconds         | ~60 minutes        | ~15 minutes        |
| Energy usage          | Negligible           | Negligible           | Negligible          | Very high          | Low                |
| Open membership       | Yes                  | Partial              | No (Council-governed)| Yes               | Yes                |
| Safety priority       | Safety > Liveness    | Safety > Liveness    | Safety + Liveness   | Liveness > Safety  | Safety + Liveness  |
 
 
## 10. Official Resources
 
| Resource                                    | URL                                                                                   |
|---------------------------------------------|---------------------------------------------------------------------------------------|
| SCP Overview (Stellar Docs)                 | <https://developers.stellar.org/docs/learn/fundamentals/stellar-consensus-protocol>   |
| SCP Whitepaper (David Mazières)             | <https://stellar.org/learn/stellar-consensus-protocol>                                |
| SCP Proof and Code (SDF Blog)               | <https://stellar.org/blog/foundation-news/stellar-consensus-protocol-proof-code>      |
| Validators Introduction                     | <https://developers.stellar.org/docs/validators>                                      |
| Tier 1 Organizations                        | <https://developers.stellar.org/docs/validators/tier-1-orgs>                          |
| Validator Configuration (Quorum Sets)       | <https://developers.stellar.org/docs/validators/admin-guide/configuring>              |
| Stellar Stack Overview                      | <https://developers.stellar.org/docs/learn/fundamentals/stellar-stack>                |
 
---
 
*Module 02 of 07 - Stellar Knowledge Base*  
*Maintained by: [Miguel Fagundez](https://github.com/miguelfagundez) & [Nemorix Group, LLC](https://github.com/nemorixgroup)*
