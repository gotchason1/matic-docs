---
id: commitments
title: Commitments and Nullifiers 
sidebar_label: Commitment and Nullifiers
description: Single and double transfer
keywords:
  - docs
  - polygon
  - nightfall
  - commitment
  - nullifier
image: https://wiki.polygon.technology/img/thumbnail/polygon-nightfall.png
---

## **What are Commitments?**

A commitment is a cryptographic primitive that allows a user to commit to a chosen value 
while keeping it hidden to others, with the ability to reveal the committed value later. 
Confidentiality of value and recipient is attained in this manner.

Every time a user performs a transaction using Nightfall, the browser wallet computes a Zero 
Knowledge Proof (ZKP) and creates (or nullifies) a commitment. 
For instance, you create a commitment when you make a deposit or a transfer and nullify a commitment when you 
make a transfer or a withdrawal.

ZKP computation relies on [circuits](../protocol/circuits.md) that define the rules which a 
transaction must follow to be correct. 

The commitment is used to hide the following properties:
- **ERC address of the token**
- **Token Id**
- **Value**
- **Owner**

Commitments are stored in a *Merkle Tree* structure. The root of this *Merkle Tree* is stored on-chain.

![](../imgs/commitment.png)

### **UTXO**

Commitments are created during deposits and transfers, and are spent during transfers and withdrawals transactions. **Commitments are not aggregated together**. When spending a commitment, the value of the commitment spent is limited to the value of up to any two commitnents owned. 

Current ZKP transfer and withdraw circuits used in Nightfall are restricted to 4 inputs, which are used to select transfer and withdrawal amounts and the fees to the proposer.  If a transactor's set of commitments contain primarily low value commitments (dust), they may find it hard to conduct future transfers as it may not be possible to combine different commitments to obtain the target transfer/withdrawal amount.

In order to select the best suitable commitments, the algorithm:
1. Gets all the user-owned commitments related to the transaction `ercAddress` in ascending order
2. Verify there exists a combination of up to 4 commitments that suits the target transaction
3. Select the commitments used, giving priority to the smallest ones

Observe the following value sets:

- **Set A**: [1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
- **Set B**: [2, 2, 2, 2, 2]
- **Set C**: [2, 8]

While all three sets have equivalent total sums, the maximum value transfer that can be transacted by sets *A*, *B*, and *C* are 4, 8, and 10 respectively. This is one of the reasons why large commitment values are preferred. The commitment selection strategy used mitigates this risk by prioritising the use of small value commitments while also minimising the creation of dust commitments.


## **What are Nullifiers?**

A **Nullifier** is the result of combination of a commitment and the nullifier key. They are stored on-chain as part of the call data during block proposal. Once the nullifier is broadcasted on-chain, the commitment is considered spent.

![](../imgs/nullifier.png)
