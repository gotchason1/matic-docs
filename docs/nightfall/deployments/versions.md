---
id: versions
title: History
sidebar_label: History of Changes
description: Deployed versions of Polygon Nightfall
keywords:
  - docs
  - polygon
  - nightfall
  - changelog
  - versions
image: https://wiki.polygon.technology/img/thumbnail/polygon-nightfall.png
---

## **Beta 1.0**

Beta 1.0 was released on May 17th, 2022. It included the basic mechanism to have Nightfall proof of concept deployed. It supported a single `Proposer` and first `coarse` implementation of transactions. It also provided a preliminary version of a user wallet.

Deployed version can be found [here](https://github.com/EYBlockchain/nightfall_3/commit/bc3e475de3e2877f14430f9599e5b38ea960765b).

## **Beta 2.0**

Deployed version can be found [here](https://github.com/EYBlockchain/nightfall_3/commit/4c2af01ac95af5ea6f5b40071d73a1624f06ba46).

Several improvements have been made:

- **Collecting Fees**: Ability for proposers to collect fees for transactions they collect. This feature will incentivize multiple proposers.
- **Challenger Deployment**: Network now includes a challenger monitoring the proposer's blocks.
- **Circuits Transactions**: More flexibility and better performance when generating the ZKP of any transactions.  Both `transfer` and `withdrawal` accept up to two commitments and return change.

  Regarding performance, transactions are optimized to make sure it takes **shorter time to proof**.

  | Operations| Constraints before | Constrains after | 
  |-----------|--------------------|------------------|
  | Deposit   | 84,766             |  1277            |
  | Transfer  | 499,119	         | 67,694           |
  | Withdraw  |  168,883           | 53,348           |

  Part of the optimization has been possible by switching to Poseidon.

- **Secrets encryption**: We have moved from El-Gamal to a [KEM-DEM](../protocol/secrets) encryption process which has resulted in several benefits:
  - Simplification of the encryption/decryption process
  - Reduction in the constraints and on-chain gas costs

## **Beta 2.1**

Deployed version can be found [here](https://github.com/EYBlockchain/nightfall_3/commit/2a42e9a0e820c0bb14ff619aeda57686d7071d51).

**General**

- Update Nightfall to Node v16.17 and npm v8.15
- Update to ZoKrates v0.8.2

**Gas optimizations**

- Transactions' merkle tree includes a variable height depending on the number of transactions.
- Use `calldata` to store input parameters of solidity calls whenever possible.

**Circuit optimizations**

- 20% reduction (~12K) in the number of constraints in transfer and withdrawal transactions

**Commitment optimizations**

- Combine 4 commitment slots in transaction structure so that the transactor can use all unused slots for transfer and withdrawal and fee payment without any pre-established combination. For example, a transactor could use 1 commitment slot for fee payment and 3 commitment slots for transfer value..

A comprehensive list of changes can be found [here](https://github.com/EYBlockchain/nightfall_3/releases/tag/v4.0.0beta01).
