---
id: verifier-overview
title: "Verifier Overview"
sidebar_label: Overview
description: "Verifier main concepts."
keywords: 
  - docs
  - polygon
  - id
  - verifier
image: https://wiki.polygon.technology/img/thumbnail/polygon-id.png
---

import useBaseUrl from '@docusaurus/useBaseUrl';

A Verifier is any web2 or web3 platform that wants to authenticate users based on their claims. Verifiers can set up queries based on **users’ existing claims** collected from a broad set of [Issuers](../issuer/issuer-overview). A [Query](./verification-library/zk-query-language) encapsulates the criteria that a user must match to authenticate, such as “must be a member of XYZ DAO” or “must be over 18 years old”. Polygon ID provides a seamless, customized and privacy-based authentication experience to users.

The request of the Verifier is encapsulated into a QR code and shown to the user. The user scans the QR code with its [Wallet](../wallet/wallet-overview.md) to prompt the zero knowledge proof generation. 

The verification process doesn’t involve any interaction between the Verifier and the Issuer of the requested claim. As part of the Query, the Verifier includes the identifiers of the trusted issuers. For example, a Verifier should add XYZ DAO as the only trusted Issuer when verifying that an individual is a member of XYZ DAO. XYZ DAO doesn’t need to accept nor interact with the Verifier.

At the end of the process the Verifier gets a cryptographic proof that the user satisfies the query, while the user shares just the minimum possible amount of data required for the interaction.

<div align="center">
<img src= {useBaseUrl("img/polygonid/tot-verifier.png")} align="center" />
</div>


## On-chain and Off-chain verification

The process of verifying user information based on their claims can happen on-chain via a smart contract or off-chain. Both processes involve the same level of user privacy and the same degree of query customization. The proof generated on mobile is the same for both cases; the only difference is in the verification process. The on-chain verification happens programmatically inside a smart contract. The off-chain verification happens inside a server that needs to be set up by the Verifier application:

- [Off-chain Verification](./verification-library/verifier-library-intro) provides all the elements to create a customized Query, set up a verifier server and generate a QR on the client side to interact with the wallet.
- [On-chain Verification](./on-chain-verification/overview) allows Dapps to verify users' claim inside a Smart Contract using zero knowledge proof cryptography. 
