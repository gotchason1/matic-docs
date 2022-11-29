---
id: api3
title: API3
sidebar_label: API3
description: API3 is a first-party oracle that delivers more security, efficiency, and data-source transparency.
keywords:
  - wiki
  - polygon
  - api3
  - oracle
  - off-chain data
image: https://wiki.polygon.technology/img/polygon-wiki.png
---

import useBaseUrl from '@docusaurus/useBaseUrl';

## Overview

[API3](https://api3.org) is a collaborative project to **deliver traditional API services to smart contract platforms** in a decentralized and trust-minimized way. It is governed by a decentralized autonomous organization (DAO), namely the [API3 DAO](https://api3.org/dao).

## First-party oracles

An [Airnode](https://docs.api3.org/airnode) is a **first-party oracle** that pushes off-chain API data to your on-chain contract. Airnode lets API providers easily run their own oracle nodes. That way, they can provide data to any on-chain dApp that's interested in their services, all without an intermediary.

An on-chain smart contract makes a request in the [**RRP protocol contract (AirnodeRrpV0.sol)**](https://docs.api3.org/airnode/v0.9/concepts/) that adds the request to the event logs. The Airnode then accesses the event logs, fetches the API data and performs a callback to the requester with the requested data.

## Requesting off-chain data by calling an Airnode

Requesting off-chain data essentially involves triggering an Airnode and getting its response
through your smart contract. The smart contract in this case would be the
requester contract which will make a request to the desired off-chain Airnode
and then capture its response.

The requester calling an Airnode primarily focuses on two tasks:

- **Make the request**
- **Accept and decode the response**

Here is an example of a basic requester contract to request data from an Airnode:

```
pragma solidity 0.8.9;

import "@api3/airnode-protocol/contracts/rrp/requesters/RrpRequesterV0.sol";

// A Requester that will return the requested data by calling the specified airnode.
// Make sure you specify the right _rrpAddress for your chain.

contract Requester is RrpRequesterV0 {
    mapping(bytes32 => bool) public incomingFulfillments;
    mapping(bytes32 => int256) public fulfilledData;

    constructor(address _rrpAddress) RrpRequesterV0(_rrpAddress) {}

    /**
     * The main makeRequest function that will trigger the Airnode request
     * airnode: Airnode address
     * endpointId: The endpoint ID for the specific endpoint
     * sponsor: The requester contract itself (in this case)
     * sponsorWallet: The wallet that will make the actual request (needs to be funded)
     * parameters: encoded API parameters
     */
    function makeRequest(
        address airnode,
        bytes32 endpointId,
        address sponsor,
        address sponsorWallet,
        bytes calldata parameters
        
    ) external {
        bytes32 requestId = airnodeRrp.makeFullRequest(
            airnode,
            endpointId,
            sponsor,
            sponsorWallet,
            address(this),
            this.fulfill.selector,
            parameters
        );
        incomingFulfillments[requestId] = true;
    }

    // The callback function with the requested data
    function fulfill(bytes32 requestId, bytes calldata data)
        external
        onlyAirnodeRrp
    {
        require(incomingFulfillments[requestId], "No such request made");
        delete incomingFulfillments[requestId];
        int256 decodedData = abi.decode(data, (int256));
        fulfilledData[requestId] = decodedData;
    }
}
```

The `_rrpAddress` is the main `airnodeRrpAddress`. The RRP Contracts have already been deployed on-chain. You can check the address for Polygon [here](https://docs.api3.org/airnode/v0.9/reference/airnode-addresses.html). You can also try [**deploying it on Remix**](https://remix.ethereum.org/#url=https://github.com/vanshwassan/RemixContracts/blob/master/contracts/Requester.sol&optimize=false&runs=200&evmVersion=null&version=soljson-v0.8.9+commit.e5eed63a.js)

### Request parameters

The `makeRequest()` function expects the following parameters to make a valid request.

- [`airnode`](https://docs.api3.org/airnode/v0.9/concepts/airnode.html): Specifies the Airnode Address.
- [`endpointId`](https://docs.api3.org/airnode/v0.9/concepts/endpoint.html): Specifies which endpoint to be used.
- [`sponsor`](https://docs.api3.org/airnode/v0.9/concepts/sponsor.html) and [`sponsorWallet`](https://docs.api3.org/airnode/v0.9/concepts/sponsor.html#sponsorwallet): Specifies which wallet will be used to fulfill the request.
- [`parameters`](https://docs.api3.org/ois/v1.2/reserved-parameters.html): Specifies the API and Reserved Parameters (see [Airnode ABI specifications](https://docs.api3.org/airnode/v0.9/reference/specifications/airnode-abi-specifications.html) for how these are encoded). Parameters can be encoded off-chain using `@airnode-abi` library.

### Response parameters

The callback to the **Requester** contains two parameters:

- [`requestId`](https://docs.api3.org/airnode/v0.9/concepts/request.html#requestid): First acquired when making the request and passed here as a reference to identify the request for which the response is intended.
- `data`: In case of a successful response, this is the requested data which has been encoded and contains a timestamp in addition to other response data. Decode it using the `decode()` function from the `abi` object.

## Using API3 QRNG 

[API3 QRNG](https://docs.api3.org/qrng/) is a public utility we provide with the courtesy of [Australian National University (ANU)](https://www.anu.edu.au/). It is powered by an Airnode hosted by [ANU Quantum Random Numbers](https://quantumnumbers.anu.edu.au/), meaning that it is a first-party service.
It is served as a public good and is free of charge (apart from the gas costs), and it **provides ‘true’ quantum randomness** via an easy-to-use solution when requiring RNG on-chain.

To request randomness on-chain, the requester submits a request for a random number to `AirnodeRrpV0`. The ANU Airnode gathers the request from the `AirnodeRrpV0` protocol contract, retrieves the random number off-chain, and sends it back to `AirnodeRrpV0`. Once received, it performs a callback to the requester with the random number.

### QRNG Getting Started 

Here is an example of a basic `QrngRequester` that requests a random number:

```
//SPDX-License-Identifier: MIT
pragma solidity 0.8.9;
import "@api3/airnode-protocol/contracts/rrp/requesters/RrpRequesterV0.sol";

contract RemixQrngExample is RrpRequesterV0 {
    event RequestedUint256(bytes32 indexed requestId);
    event ReceivedUint256(bytes32 indexed requestId, uint256 response);

    address public airnode;
    bytes32 public endpointIdUint256;
    address public sponsorWallet;
    mapping(bytes32 => bool) public waitingFulfillment;

    // These are for Remix demonstration purposes, their use is not practical.
    struct LatestRequest { 
      bytes32 requestId;
      uint256 randomNumber;
    }
    LatestRequest public latestRequest;

    constructor(address _airnodeRrp) RrpRequesterV0(_airnodeRrp) {}

    // Normally, this function should be protected, as in:
    // require(msg.sender == owner, "Sender not owner");
    function setRequestParameters(
        address _airnode,
        bytes32 _endpointIdUint256,
        address _sponsorWallet
    ) external {
        airnode = _airnode;
        endpointIdUint256 = _endpointIdUint256;
        sponsorWallet = _sponsorWallet;
    }

    function makeRequestUint256() external {
        bytes32 requestId = airnodeRrp.makeFullRequest(
            airnode,
            endpointIdUint256,
            address(this),
            sponsorWallet,
            address(this),
            this.fulfillUint256.selector,
            ""
        );
        waitingFulfillment[requestId] = true;
        latestRequest.requestId = requestId;
        latestRequest.randomNumber = 0;
        emit RequestedUint256(requestId);
    }

    function fulfillUint256(bytes32 requestId, bytes calldata data)
        external
        onlyAirnodeRrp
    {
        require(
            waitingFulfillment[requestId],
            "Request ID not known"
        );
        waitingFulfillment[requestId] = false;
        uint256 qrngUint256 = abi.decode(data, (uint256));
        // Do what you want with `qrngUint256` here...
        latestRequest.randomNumber = qrngUint256;
        emit ReceivedUint256(requestId, qrngUint256);
    }
}
```

- The `setRequestParameters()` takes in `airnode` (The ANU Airnode address) , `endpointIdUint256`, `sponsorWallet` and sets these parameters. You can get Airnode address and the endpoint ID [here](https://docs.api3.org/qrng/reference/providers.html).
- The `makeRequestUint256()` function calls the `airnodeRrp.makeFullRequest()` function of the `AirnodeRrpV0.sol` protocol contract which adds the request to its storage and returns a `requestId`.
- The targeted off-chain ANU Airnode gathers the request and performs a callback to the requester with the random number.

You can read more about API3 QRNG [here](https://docs.api3.org/qrng/).

### [Try deploying it on Remix!](https://remix.ethereum.org/#url=https://github.com/vanshwassan/RemixContracts/blob/master/contracts/QrngRequester.sol&optimize=false&runs=200&evmVersion=null&version=soljson-v0.8.9+commit.e5eed63a.js)

 ## Decentralized price feeds
[dAPIs](https://docs.api3.org/dapis/) are decentralized price feeds engrained with risk protection. Using a first-party architecture dAPIs provide a transparent, secure, and scalable price feed solution. 

Developers can access dAPIs through the [API3 Market](https://bit.ly/api3marketpolywiki), where data feeds are easily searched, monitored, and consumed. 

You can read more about dAPIs [here](https://docs.api3.org/dapis/). 

## ChainAPI 

[ChainAPI](https://chainapi.com/) is the Web3 data integration platform that provides businesses with all the tools they need to connect their data to blockchain-based applications.
API providers can follow a simple GUI-based integration and deployment flow and start running their first-party Airnode without writing a single line of code.

If you have a REST API, you can easily deploy your own Airnode using [ChainAPI](https://chainapi.com) to Polygon. 

Check out the API3 Docs [here](https://docs.api3.org).
