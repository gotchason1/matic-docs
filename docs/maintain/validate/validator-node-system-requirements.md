---
id: validator-node-system-requirements
title: System Requirements
description: System requirements to run a validator node
keywords:
  - docs
  - matic
  - polygon
  - prerequisites
  - requirements
slug: validator-node-system-requirements
image: https://wiki.polygon.technology/img/polygon-wiki.png
---

The system requirements listed in this section are both for the [Sentry](/docs/maintain/glossary.md#sentry) node and the [Validator](/docs/maintain/glossary.md#validator) node.

The **minimum** system requirements mean you can run the nodes but the setup is not future-proof.

The **recommended** system requirements mean the nodes are future-proof. There is, however, no upper limit to future-proofing your nodes.

You must always run the sentry node and the validator node on separate machines.

## Minimum system requirements

* RAM: 32 GB
* CPU: 8-core
* Storage: 2.5 TB SSD

:::info

For Amazon Web Services (AWS), the equivalent of the minimum requirements instances are **m5d.2xlarge** or **t3.2xlarge** with unlimited credits selected.

For storage, make sure the 2.5 TB SSD storage is extendable.

:::

## Recommended system requirements

* RAM: 64 GB
* CPU: 16-core
* Storage: 5 TB SSD
* Bandwidth: 1 Gbit/s

:::info

For Amazon Web Services (AWS), the equivalent of the recommended requirements instance is **m5d.4xlarge**.

For OVH, the equivalent of the recommended requirements instance is **infra-3**.

For network, expect 3-5 TB of data transferred per month.

:::
