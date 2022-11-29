---
id: custom-tokens
title: Adding Custom Tokens in Venly (prev. Arkane)
sidebar_label: Custom Tokens
description: Support for custom ERC20/ERC721/ERC1155 tokens on Polygon network
keywords:
  - wiki
  - polygon
  - custom tokens
  - nfts
  - venly
  - wallet
image: https://wiki.polygon.technology/img/polygon-wiki.png
---

## Fungible Tokens

A developer can easily add support for their custom ERC20 token by creating a small pull request containing the token details toward the [Arkane Git repository](https://github.com/ArkaneNetwork/content-management/tree/master/tokens). Here is an example snippet of the information you must provide:

```
{"name":"SAND","symbol":"SAND","address":"0x3845badade8e6dff049820680d1f14bd3903a5d0","decimals":18,"type":"ERC20"}
```

You can always contact the Venly team via their in-app chat and ask them to add your custom fungible token.

## Non-Fungible Tokens (NFTs)

Venly has developed its service in such a way that it will automatically pick up custom created Non-Fungible Tokens (NFTs) if they follow the ERC721 and ERC1155 standard. This makes Venly the only wallet platform to show all NFTs that are live on the Polygon network.

![The Hulk ERC1155 NFT on Polygon](img/06.png)
