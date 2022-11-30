---
id: lx-ly-bridge
title: LX-to-LY Bridge
sidebar_label: LX-to-LY Bridge
description: Introduction to Lx-to-Ly bridges on zk rollups
keywords:
  - docs
  - zk rollups
  - polygon
  - proof
  - efficiency
  - hermez
  - bridge
  - zkEVM
  - Polygon zkEVM
image: https://wiki.polygon.technology/img/thumbnail/polygon-zkevm.png
---

An LX-LY bridge is a Smart Contract that lets users transfer their assets between two layers, LX and LY. The L1-L2 in zkEVM is a decentralised bridge for secure deposits and withdrawal of assets. It is a combination of two smart contracts, one deployed on one chain and the second on the other.

The LX-LY Bridge forms the architecture for transferring assets among multiple zkRollups as well as L1 (Ethereum).

## Bridge L1 Contract

Bridge L1 Contract carries out two operations: `bridge` and `claim`. The `bridge` operation transfers assets from one rollup to another, while the `claim` operation applies when the contract makes a claim from any rollup.

Bridge L1 Contract requires two Merkle trees in order to perform the above operations: `globalExitTree` and `mainnet exit tree`. The `globalExitTree` contains all the information of exit trees of all rollups, whereas the `mainnet exit tree` has information on transactions made by users who interact with the mainnet. A contract named the **global exit root manager L1** is responsible for managing exit roots across multiple networks. 

The exit tree structure is depicted in the diagram below:

![The Exit Tree Structure](figures/fig6-exit-tr-strct.png)

## Bridge L2 Contract

Bridge L2 Contract is deployed on Layer L2 with ether on it. The ether will be set on the genesis in order to enable the minting / burning of the native ether.

Bridge L2 Contract also requires all the information of exit trees of all rollups contained in the `globalExitTree` Merkle tree. In this case, a smart contract named the **global exit root manager L2** is responsible for managing the exit roots across multiple networks.

:::info

When a batch is verified in the PoE smart contract in L1, the rollup exit root is updated in the global exit root manager L1. Bridge L2 Contract handles the rollup side of the `bridge` and the `claim` operations, as well as interacting with the `globalExitTree` and the `rollup exit tree`, mainly to update exit roots.

:::

## LX-to-LY Bridge

Typically, a bridge smart contract is an L2-to-L1 Bridge, but the zkEVM Bridge is more flexible and interoperable. It can function as a bridge between any two arbitrary Layer 2 chains, L2_A and L2_B, or between any Layer 2 (say L2_X) and L1 (Ethereum blockchain). It consequently allows asset transfers among multiple rollups.