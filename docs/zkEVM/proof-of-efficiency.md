---
id: proof-of-efficiency
title: Proof of Efficiency
sidebar_label: Proof of Efficiency
description: Detailed description of Proof of Efficiency consensus mechanism
keywords:
  - docs
  - zk rollups
  - Polygon zkEVM
  - polygon
  - proof
  - efficiency
  - hermez
  - zkEVM
image: https://wiki.polygon.technology/img/thumbnail/polygon-zkevm.png
---

## Background

In zk-Rollups, the challenge of decentralizing is huge and it has been difficult to find a good solution so far. This happens because protocols such as PoS have some issues on L2 and there’s a need to get production of zero-knowledge validity proofs (which are very computation intensive for the prover) with high performance so the network can keep its service level.

Our earlier **Proof of donation (PoD)** mechanism was based on a decentralized auction model to get the right to produce batches in a specific timeframe. In this mechanism, the economic incentives were set up so the validators need to be very efficient in order to be competitive.

The latest **Proof of Efficiency (PoE)** consensus mechanism leverages the experience of the existing PoD in v1.0 and add support for the permissionless participation of multiple coordinators to produce batches in L2.

Let's explore in detail why we chose PoE over PoD consensus mechanism.

## Why PoD is not the Best Option

The PoD model fell out of our preferable options for the reasons listed below:

- PoD has proven to be rather complex for both coordinators and validators. Furthermore, it has proven to be less economically viable.
- This consensus technique is susceptible to attacks, particularly during the bootstrapping phase. A permissionless participant controls the network at any given time. This increases the possibility of service level delays on the network if a third party turns malicious or encounters operational troubles.
- The effectiveness of selecting "the best" operator amounts to a "winner-takes-all" strategy, which is unjust to competitors with somewhat lower performance. Consequently, a few select operators validate batches more frequently than others, rendering network decentralisation obsolete.

## Why is PoE a Better Model?

The **Proof of Efficiency (PoE)** model leverages the existing PoD mechanism and supports the permissionless participation of multiple coordinators to produce batches in Layer L2. These batches are created from the rolled-up transactions of Layer 1. PoE employs a simpler technique and is favoured due to its greater efficiency in resolving the challenges involved in PoD.  

The strategic implementation of PoE promises to ensure that the network: 

- Maintains its **Permissionless** feature to produce L2 batches 
- Is efficient, a criterion which is key for the overall network performance
- Attains an acceptable degree of decentralization
- Is protected from malicious attacks, especially by validators
- Maintains a fair balance between overall validation effort and network value

:::tip Good to Know

Possibilities of coupling PoE with a PoS (Proof of Stake) are currently being explored. A detailed description of zkEVM's PoE by [<ins>David</ins>](https://twitter.com/davidsrz) and [<ins>Jordi</ins>](https://twitter.com/jbaylina) is published on [<ins>Ethereum Research</ins>](https://ethresear.ch/t/proof-of-efficiency-a-new-consensus-mechanism-for-zk-rollups/11988).

:::

## On-Chain Data Availability

A **Full ZK-Rollup** schema requires the publication of both **the data** (which users need to reconstruct the full state) and **the validity proofs** (zero-knowledge proofs) on-chain. However, given the Ethereum configuration, publishing data on-chain incurs gas prices, which is an issue with Layer 1. This makes deciding between a Full ZK-Rollup configuration and a Hybrid configuration challenging.

Under a Hybrid schema, either of the following is possible:

 - **Validium**: Data is stored off-chain and only the validity proofs are published on-chain.
 - **Volition**: For some transactions, both the data and the validity proofs remain on-chain while for the remaining ones, only proofs go on-chain.

Unless, among other things, the proving module can be highly accelerated to mitigate costs for the validators, a Hybrid schema remains viable.

## The PoE Smart Contract

The underlying protocol in zkEVM ensures that the state transitions are correct by employing a validity proof. To ensure that a set of pre-determined rules have been followed for allowing state transitions, the Proof of Efficiency smart contract is employed.

:::info

The Proof of Efficiency (PoE) smart contract is currently deployed on the Goerli testnet. It is available [here](https://goerli.etherscan.io/address/0x14cB06e8dE2222912138F9a062E5a4d9F4821409).

:::

A smart contract verifies the validity proofs to ensure that each transition is completed correctly. This is accomplished by employing zk-SNARK circuits. A system of this type requires two processes: **transaction batching** and **transaction validation**.

To carry out these procedures, zkEVM employs two sorts of participants: **Sequencers** and **Aggregators**. Under this two-layer model: 

- [**Sequencers**](/docs/zkEVM/zknode/overview.md#sequencers) &rarr; propose transaction batches to the network, i.e. they roll-up the transaction requests in batches and add them to the PoE Smart Contract.

- [**Aggregators**](/docs/zkEVM/zknode/overview.md#aggregators) &rarr; check the validity of the transaction batches and provide validity proofs. Any permissionless Aggregator can submit the proof to demonstrate the correctness of the state transition computation.

The PoE Smart Contract, therefore, makes two calls: One to receive batches from Sequencers, and another to Aggregators, requesting batches to be validated.

![Figure 2: Simplified Proof of Efficiency](figures/fig2-simple-poe.png)

## Proof of Efficiency Tokenomics

The PoE Smart Contract imposes the following requirements on Sequencers and Aggregators:

### Sequencers

- Anyone with the software necessary for running a zkEVM node can be a Sequencer. 
- Every Sequencer must pay a fee in form of MATIC tokens to earn the right to create and propose batches. 
- A Sequencer that proposes valid batches (which consist of valid transactions), is incentivised with the fee paid by transaction-requestors or the users of the network. 


### Aggregators

An Aggregator receives all the transaction information from the Sequencer and sends it to the prover which provides a small zk-proof after complex polynomial computations. The smart contract validates this proof. This way, an aggregator collects the data, sends it to the prover, receives its output and finally, sends the information to the smart contract to check that the validity proof from the prover is correct. 

- An Aggregator's task is to provide validity proofs for the L2 transactions proposed by Sequencers.
- In addition to running zkEVM's zkNode software, Aggregators need to have specialised hardware for creating the zero-knowledge validity proofs utilizing zkProver.
- For a given batch or batches, an Aggregator that submits a validity proof first earns the MATIC fee (which is being paid by the Sequencer(s) of the batch(es)).
- The Aggregators need to indicate their intention to validate transactions. After that, they compete to produce validity proofs based on their own strategy.