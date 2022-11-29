---
title: Tellor
description: "A guide to integrate the Tellor oracle into your Polygon contract."
author: "Tellor"
lang: en
sidebar: true
tags: ["solidity", "smart contracts", "price feeds", "oracles", "Polygon", "Matic", "Tellor"]
skill: beginner
published: 2022-02-10
source: Tellor Docs
sourceUrl: https://docs.tellor.io/tellor/
---

Tellor is an oracle that provides censorship resistant data that is secured by simple crypto-economic incentives. Data can be provided by anyone and checked by everyone. Tellor’s flexible structure can provide any data at any time interval to allow for easy experimentation/innovation.

## (Soft) Prerequisites

We're assuming the following about your coding skill-level to focus on the oracle aspect.

Assumptions:

- you can navigate a terminal
- you have npm installed
- you know how to use npm to manage dependencies

Tellor is a live and open-sourced oracle ready for implementation. This beginner's guide is here to showcase the ease with which one can get up and running with Tellor, providing your project with a fully decentralized and censorship-resistant oracle.

## Overview

Tellor is an oracle system where parties can request the value of an off-chain data point (e.g. BTC/USD) and reporters compete to add this value to an on-chain data-bank, accessible by all Polygon smart contracts. The inputs to this data-bank are secured by a network of staked reporters. Tellor utilizes crypto-economic incentive mechanisms. Honest data submissions by reporters are rewarded by the issuance of Tellor’s token. Any bad actors are quickly punished and removed from the network by a dispute mechanism.

In this tutorial we'll go over:

- Setting up the initial toolkit you'll need to get up and running.
- Walk through a simple example.
- List out testnet addresses of networks you currently can test Tellor on.

## UsingTellor

The first thing you'll want to do is install the basic tools necessary for using Tellor as your oracle. Use [this package](https://github.com/tellor-io/usingtellor) to install the Tellor User Contracts:

`npm install usingtellor`

Once installed this will allow your contracts to inherit the functions from the contract 'UsingTellor'.

Great! Now that you've got the tools ready, let's go through a simple exercise where we retrieve the bitcoin price:

### BTC/USD Example

Inherit the UsingTellor contract, passing the Tellor address as a constructor argument:

Here's an example:

```solidity
import "usingtellor/contracts/UsingTellor.sol";

contract PriceContract is UsingTellor {

  uint256 public btcPrice;

  //This Contract now has access to all functions in UsingTellor

  constructor(address payable _tellorAddress) UsingTellor(_tellorAddress) public {}

  function setBtcPrice() public {

    bytes memory _b = abi.encode("SpotPrice",abi.encode("btc","usd"));
    bytes32 _queryID = keccak256(_b);

    bool _didGet;
    uint256 _timestamp;
    bytes _value;

    (_value, _timestamp) = getDataBefore(_queryId, block.timestamp - 15 minutes);

    btcPrice = abi.decode(_value,(uint256));
  }
}
```

## Addresses:

Tellor Tributes: [`0xe3322702bedaaed36cddab233360b939775ae5f1`](https://polygonscan.com/token/0xe3322702bedaaed36cddab233360b939775ae5f1#code)

Oracle: [`0x8f55D884CAD66B79e1a131f6bCB0e66f4fD84d5B`](https://polygonscan.com/address/0x8f55D884CAD66B79e1a131f6bCB0e66f4fD84d5B#code)

#### Looking to do some testing first?:

Polygon Mumbai Testnet: [`0x8f55D884CAD66B79e1a131f6bCB0e66f4fD84d5B `](https://mumbai.polygonscan.com/address/0x8f55D884CAD66B79e1a131f6bCB0e66f4fD84d5B/contracts#code)

Test Tributes: [`0xCE4e32fE9D894f8185271Aa990D2dB425DF3E6bE`](https://mumbai.polygonscan.com/token/0xCE4e32fE9D894f8185271Aa990D2dB425DF3E6bE#code)

Need some test tokens? Tweet us at ['@trbfaucet'](https://twitter.com/trbfaucet)

For ease of use, the  UsingTellor  repo comes with a version of the [Tellor Playground](https://github.com/tellor-io/TellorPlayground) contract for easier integration. See [here](https://github.com/tellor-io/sampleUsingTellor#tellor-playground) for a list of helpful functions.

#### For a more robust implementation of the Tellor oracle, check out the full list of available functions [here.](https://github.com/tellor-io/usingtellor/blob/master/README.md)

#### Still have questions? Join the community [here!](https://discord.gg/tellor)
