---
id: getting-started
title: Introduction to Polygon PoS
sidebar_label: Quick Start
description: Build your next blockchain app on Polygon.
keywords:
  - docs
  - matic
  - polygon
  - build on polygon
  - blockchain
  - introduction
  - how to launch dapp
  - dapps
  - ethereum
image: https://wiki.polygon.technology/img/polygon-wiki.png
---

:::caution Updating the Develop Docs

The docs are being updated, enhanced, and improved. They are subject to change.
Please feel free to raise an issue or pull request if you have any queries or suggestions.

:::

Welcome to **Polygon (previously Matic Network)**! The most innovative and exciting platform to develop your blockchain application. Blockchain technology is poised to revolutionize how the digital world manages data and conducts business. You can join this revolution by getting a head start on Polygon's decentralized application (dApp) development.

This guide will introduce you to the Polygon ecosystem. You'll find links to valuable resources and websites that will bring you up to speed on building, not only on Polygon but also on general blockchain application development.

:::tip Stay in the know

Keep up with the latest builder updates from the Polygon 
team and the community by subscribing to the 
[<ins>Polygon notification groups</ins>](https://polygon.technology/notifications/).

:::

## Key Features of Polygon

- **Speed**: The Polygon Network uses a high-throughput blockchain with consensus provided by a group of Block Producers selected by stakeholders at each checkpoint. A Proof of Stake layer is used to validate blocks and periodically post proofs of Block Producers to the Ethereum mainnet. This enables rapid block confirmation rates of about 2 seconds while preserving a high amount of decentralization, resulting in excellent throughput for the network.
- **Scalability**: Polygon Network achieves a hypothetical transaction speed of fewer than 2 seconds on a single sidechain. Using multiple sidechains helps the network to handle millions of transactions per second. This mechanism (already demonstrated in the first Matic sidechain) allows the Polygon network to scale easily.
- **Security**: Polygon's smart contracts rely on Ethereum’s security. To safeguard the network, it employs three critical security models. It uses Ethereum's **staking management contracts** and a group of incentivized validators running **Heimdall** and **Bor** nodes. Developers can also implement both models (Hybrid) into their dApp.

## Building on Polygon

If you are an Ethereum developer, you are already a Polygon developer. Simply switch to the [Polygon RPC](https://polygon-rpc.com/) and get started. All the tools you are familiar with on the Ethereum blockchain are supported on Polygon by default, such as Truffle, Remix, and Web3js.

You can deploy decentralized applications to either Polygon Mumbai Testnet or the Mainnet. The Polygon Mumbai Testnet connects with the Ethereum Goërli Testnet, which acts as its ParentChain. You can find all the network-related details in the [network documentation](https://github.com/maticnetwork/matic-docs/blob/master/docs/develop/network-details/network.md).

### Wallets

To interact with the Polygon Network, you need to have an Ethereum-based wallet because Polygon runs on Ethereum Virtual Machine (EVM). You can choose to set up a [Metamask](https://github.com/maticnetwork/matic-docs/blob/master/docs/develop/metamask/overview.md) or [Arkane](https://github.com/maticnetwork/matic-docs/blob/master/docs/develop/wallets/arkane/intro_arkane.md) Wallet. More on wallet-related information and why you need one can be found in our [wallet documentation](https://docs.polygon.technology/docs/develop/wallets/getting-started).

### Smart Contracts

Polygon supports many services you can use to test, compile, debug, and deploy decentralized applications onto the Polygon Network. These include deployment using [Alchemy](https://github.com/maticnetwork/matic-docs/blob/master/docs/develop/alchemy.md), [Chainstack](https://github.com/maticnetwork/matic-docs/blob/master/docs/develop/chainstack.md), [QuickNode](https://github.com/maticnetwork/matic-docs/blob/master/docs/develop/quicknode.md), [Remix](https://github.com/maticnetwork/matic-docs/blob/master/docs/develop/remix.md), [Truffle](https://github.com/maticnetwork/matic-docs/blob/master/docs/develop/truffle.md), [Hardhat](https://github.com/maticnetwork/matic-docs/blob/master/docs/develop/hardhat.md), and [Replit](https://github.com/maticnetwork/matic-docs/blob/master/docs/develop/replit.md).

### Connecting to Polygon

You can add Polygon to Metamask or directly use Arkane, which allows you to connect to Polygon using [RPC](https://docs.polygon.technology/docs/develop/metamask/config-polygon-on-metamask/).

In order to connect with the Polygon network to read blockchain information, we recommend using the Alchemy SDK.

```js
// Javascript
// Setup: npm install alchemy-sdk
const { Alchemy, Network } = require("alchemy-sdk");

const settings = {
  apiKey: "demo", // Can replace with your API Key from https://www.alchemy.com
  network: Network.MATIC_MAINNET, // Can replace with MATIC_MUMBAI
};

const alchemy = new Alchemy(settings);

async function main() {
  const latestBlock = await alchemy.core.getBlockNumber();
  console.log("The latest block number is", latestBlock);
}

main();
```

### Building a new dApp on Polygon?

Decentralized applications (dApps) act as the bridge between users and their data privacy on the blockchain. The increasing number of dApps validates their usefulness within the blockchain ecosystem, solving challenges like executing transactions between two participants without the need for central authority via smart contracts.

Suppose you have no prior experience building decentralized applications (dApps). In that case, the below-mentioned resources will give you a head start on the tools required to build, debug, and deploy dApps on the Polygon Network.

- [Full Stack dApp: Tutorial Series](https://kauri.io/full-stack-dapp-tutorial-series/5b8e401ee727370001c942e3/c)
- [Web3.js](https://www.dappuniversity.com/articles/web3-js-intro)
- [Ethers.js](https://docs.ethers.io/v5/)
- [Remix](https://docs.polygon.technology/docs/develop/remix/)
- [Truffle](https://docs.polygon.technology/docs/develop/truffle)
- [Metamask](https://docs.polygon.technology/docs/develop/metamask/overview)
- [Arkane](https://docs.polygon.technology/docs/develop/wallets/arkane/intro)
- [Develop a dApp using Fauna, Polygon and React](https://docs.polygon.technology/docs/develop/dapp-fauna-polygon-react)

### Already have a dApp?

If you already have a decentralized application (dApp) and are looking for a platform to help you scale efficiently, then you are at the right place because Polygon allows you to:

1. **Easily migrate from Ethereum Virtual Machine (EVM) based chain**: Polygon prides itself in being the ultimate Layer-2 scaling solution for Ethereum. You don't have to worry about the underlying architecture while moving or deploying your dApps to the Polygon Network as long as it is EVM-compatible.
2. **Use Polygon as a faster transaction layer**: Deploying your dApp to the Polygon Mainnet allows you to leverage Polygon as a faster transaction layer for your dApp. Additionally, you can get your tokens mapped by us. You can join our [technical discussions group](http://bit.ly/matic-technical-group) on Telegram to learn more.

## Side Note

If this is overwhelming, that’s alright! You can jump right into the action and start hacking. Here are some notes before you start diving into resources, repositories, and docs:

1. **Beware the cost of being on the bleeding edge**: Like typical niche programming, dApps and blockchain development moves very quickly. While researching, you may find complex code repositories, 404s on a documentation site, or even no documentation. Use that opportunity to reach out to us via any social media channel.
2. **The learning curve may be daunting, but the barrier to entry is low**: The community is very open and welcoming! Projects welcome pull requests from outsiders and resolve any blockers actively. We’re working on creating a better world and contribution in any form is appreciated. We’ll be grateful to onboard you into this amazing Web3 ecosystem.

:::info Stay Updated

Decentralized application development encourages network decentralization. Follow our social media handles for more insights and updates about the Polygon ecosystem. You can find the links to all the Polygon communities [here](https://polygon.technology/community/).

:::
