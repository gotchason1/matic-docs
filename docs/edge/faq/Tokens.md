---
id: tokens
title: Tokens FAQ
description: "FAQ for Polygon Edge tokens"
keywords:
  - docs
  - polygon
  - edge
  - FAQ
  - tokens
---

## Does Polygon Edge support EIP-1559?
At the moment, Polygon Edge does not support EIP-1559.

## How to set the currency(token) symbol?

The token symbol is just a UI thing, so it cannot be configured or hardcoded anywhere in the network.
But you can change it when you add the network to a wallet, like Metamask, for example.

## What happens to transactions when a chain halts?

All the transactions that haven't been processed are inside the TxPool(enqueued or promoted queue). If the chain halts(all block production stops), these transactions will never go into blocks. <br/>
This is not only the case when the chain halts. If the nodes are stopped or restarted, all the transactions that haven't been executed, and are still in TxPool, will silently get removed. <br/>
The same thing will happen to transactions when a breaking change is introduced, as it is required for the nodes to be restarted.
