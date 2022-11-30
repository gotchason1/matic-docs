---
id: setup-production-node
title: Setup Production zkNode
sidebar_label: Production zkNode
description: Detailed guide on setting up a live/production Polygon zkEVM Node using Docker.
keywords:
  - docs
  - polygon
  - zkEVM
  - zkNode
  - setup
  - production zknode
  - tutorial
  - Polygon zkEVM
image: https://wiki.polygon.technology/img/thumbnail/polygon-zkevm.png
---

Polygon zkEVM is now available on the public testnet for developers to launch smart contracts, execute transactions, and experiment with the network. This tutorial extends the exploration by allowing developers to **launch their own node on the zkEVM Public Testnet**.

Before we begin, this document is fairly technical and requires prior exposure to **Docker** and **CLI**. Post spinning up your zkNode instance, **you will be able to run the Synchronizer**.

Even though the Sequencer and Aggregator functions are still centralized at this point in development, anyone can run a Synchronizer that allows accessing a trustless RPC node.

:::caution

Currently the zkProver does not run on ARM-powered Macs. For Windows users, using WSL/WSL2 is not recommended.

Unfortunately, Apple M1 chips are not supported for now - since some optimizations on the zkProver require specific Intel instructions. This means some non-M1 computers won't work regardless of the OS, for example: AMD.

:::

## Prerequisites

This tutorial assumes that you have `docker-compose` already installed. If you need any help with the installation, please check the [official docker-compose installation guide](https://docs.docker.com/compose/install/).

### Minimum System Requirements

- 16GB RAM
- 4-core CPU

It is highly recommeneded that you create a separate folder for installing and working around the zkNode. We won't be cloning any repository (unlike [<ins>local zkNode setup</ins>](setup-local-node.md)) so it's better to create a folder before starting the zkNode setup: ```mkdir -p /$HOME/zkevm-node```

## Network Components

Here is a list of **crucial components** that are required before you can run the zkNode:

- **Ethereum Node** - L1 Network
- **zkEVM-Node (or zkNode)** - L2 Network
  - **JSON RPC Server** - Interface to L2 network
  - **Synchronizer** - Responsible for synchronizing data between L1 and L2
  - **Sequencer** - Responsible for selecting transactions from the pool and propose new batches
  - **Aggregator**  - Responsible for consolidating the changes in the state proposed by the Sequencers
- **zkProver** - Zero knowledge proof generator

Optional components:

- **Metamask** - Wallet to manage blockchain accounts
- **Block Scout Explorer** - Web UI to interact with the network information

Let's set up each of the above components!

## Ethereum Node Setup

The Ethereum Node will be the first component we will set up. It is the first because synchronizing the Ethereum network will take a long time, so we will keep it synchronized while we set up the other components to take advantage of this required time.

:::tip Before we start

There are numerous ways to set up an Ethereum L1 environment; we will use Geth for this. We recommend Geth, but any Goerli node should do.

If you plan to have more than one zkNode in your infrastructure, we advise using a machine that is specifically dedicated to this component.

:::

First of all, we need to create a folder to store the Ethereum node data outside of the Docker container, in order to not lose all the data if the container is restarted.

```bash
mkdir -p /$HOME/zkevm-node/.ethereum
```

In order to configure the Ethereum node instance, create a file called `docker-compose.yml` inside the `zkevm-node` folder:

```yml title="docker-compose.yml"
version: '3'

services:

  eth-node:
    container_name: eth-node
    image: ethereum/client-go:stable
    ports:
        - 8545:8545
        - 8546:8546
        - 30303:30303
    volumes:
        - /$HOME/zkevm-node/.ethereum:/$HOME/geth/.ethereum
    command: [
        "--goerli",
        "--http",
        "--http.addr=0.0.0.0",
        "--http.corsdomain=*",
        "--http.vhosts=*",
        "--http.api=admin,eth,debug,miner,net,txpool,personal,web3",
        "--ws",
        "--ws.addr=0.0.0.0",
        "--ws.origins=*", 
        "--graphql", 
        "--graphql.corsdomain=*", 
        "--graphql.vhosts=*", 
        "--vmdebug", 
        "--metrics",
        "--datadir=/$HOME/geth/.ethereum"
    ]
```

To run the Ethereum node instance, go to the `zkevm-node` folder in your terminal and run the following command:

```bash
docker-compose up -d
```

If you want to follow the logs of the synchronization, run the following command:

```bash
docker logs -f eth-node
```

## Postgres Setup

:::info

Running Postgres instances in a Docker container is an option but we recommend you to use a specialized infrastructure for the database, such as AWS RDS, an on-site server, or any other Postgres DB dedicated infrastructure.

It is not necessary to have a backup because all data is available on L1 and can be resynchronized if it is lost. However, having a backup is strongly advised in order to avoid resynchronizing the entire network in the event of a DB problem.

:::

In order to set up the databases for zkNode, we must set up several Postgres instances to be shared between the Node and the Prover/Executor.

- **Node requires a root access** to run the migrations and control the data.
- **Prover only needs a read-only user access** to the Merkle tree data and compute the proofs. Executor will need read/write access. Migration file `init_prover_db.sql` will create the Merkle tree table in State DB.

To achieve that, we need to create several directories for storing the Postgres data outside of the Docker container (so that we don't lose all the data if the container is restarted).

```bash
mkdir -p /$HOME/zkevm-node/.postgres-state
mkdir -p /$HOME/zkevm-node/.postgres-pool
mkdir -p /$HOME/zkevm-node/.postgres-rpc
```

Next, download [init schema for the Prover DB](https://github.com/0xPolygonHermez/zkevm-node/blob/develop/db/scripts/init_prover_db.sql) from Github and store this file in the `zkevm-node` folder.

In order to configure the Postgres instance, create a file called `docker-compose.yml` inside `zkevm-node`.

```yaml title="docker-compose.yml"
# We recommend that you customize the ENVIRONMENT variables values to your preference

version: '3'

services:
  zkevm-state-db:
    container_name: zkevm-state-db
    image: postgres
    deploy:
      resources:
        limits:
          memory: 2G
        reservations:
          memory: 1G
    ports:
      - 5432:5432
    volumes:
      - ./init_prover_db.sql:/docker-entrypoint-initdb.d/init.sql
      - /$HOME/zkevm-node/.postgres-state:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=state_user
      - POSTGRES_PASSWORD=state_password
      - POSTGRES_DB=state_db
    command: ["postgres", "-N", "500"]

  zkevm-pool-db:
    container_name: zkevm-pool-db
    image: postgres
    deploy:
      resources:
        limits:
          memory: 2G
        reservations:
          memory: 1G
    ports:
      - 5433:5432
    volumes:
      - /$HOME/zkevm-node/.postgres-pool:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=pool_user
      - POSTGRES_PASSWORD=pool_password
      - POSTGRES_DB=pool_db
    command: ["postgres", "-N", "500"]

  zkevm-rpc-db:
    container_name: zkevm-rpc-db
    image: postgres
    deploy:
      resources:
        limits:
          memory: 2G
        reservations:
          memory: 1G
    ports:
      - 5434:5432
    volumes:
      - /$HOME/zkevm-node/.postgres-rpc:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=rpc_user
      - POSTGRES_PASSWORD=rpc_password
      - POSTGRES_DB=rpc_db
    command: ["postgres", "-N", "500"]
```

To run the Postgres instance, go to the `zkevm-node` folder in your terminal and run the following command:

```bash
docker-compose up -d
```

## Executor & StateDB Setup

The zkEVM Prover is available on Docker registry. You can simply start by pulling the image:

```bash
docker pull hermeznetwork/zkevm-prover
```

Download the [sample Prover config file](https://github.com/0xPolygonHermez/zkevm-node/blob/develop/config/environments/public/public.prover.config.json) and store it as `prover-config.json` inside the `zkevm-node` folder.

Finally, add the following to the `docker-compose.yml` file:

```yaml title="docker-compose.yml"
  zkevm-prover:
    container_name: zkevm-prover
    image: hermeznetwork/zkevm-prover:develop
    ports:
      - 50061:50061 # StateDB or Merkle Tree
      - 50071:50071 # Executor
    volumes:
      - ./prover-config.json:/usr/src/app/config.json
    command: >
      zkProver -c /usr/src/app/config.json
```

This will spin up the Executor and StateDB (or Merkle Tree). For more information, check out the [zkProver repository on Github](https://github.com/0xPolygonHermez/zkevm-prover).

## zkNode Setup

After completing the above steps, we have the Postgres DBs, Prover and Ethereum Node instances running. Now is the perfect time to setup the zkNode.

:::info

The zkNode depends on Postgres, Prover and Ethereum Node instances. Make sure it has network access to them. It's advised to setup a dedicated machine for the zkNode.

:::

Before we start, the zkNode requires an Ethereum account with:

- Funds on L1 in order to propose new batches and consolidate the state
- Tokens to pay the collateral for batch proposal
- Approval of these tokens to be used by the Rollup Smart Contract on behalf of the Ethereum account owner
- Register this account as a Sequencer

We have to start with **creating a config file** to provide the configurations to the node. To do that,

- Create a file called `config.toml` inside the `zkevm-node` folder.

- Go to the [example config file available on Github](https://github.com/0xPolygonHermez/zkevm-node/blob/develop/config/environments/public/public.node.config.toml) and copy its content and paste into the `config.toml` that we just created inside `zkevm-node`.

Similarly, **create a `genesis.json` file** inside the `zkevm-node` folder and copy the contents of the sample [genesis file](https://github.com/0xPolygonHermez/zkevm-node/blob/develop/config/environments/public/public.genesis.config.json) available on Github.

:::info Remember To

- Replace the DB information if you set it differently while setting up the Postgres instance
- Set the `Database Host` with the Postgres instance IP
- Set the `Etherman URL` with the JSON RPC URL of the Ethereum node that you created earlier or use any L1 Goerli service
- Set the `Etherman Password` (`config.json` => `PrivateKeyPassword` field, defaults to `testonly`) to allow the node to decrypt the keystore file
- Set the `MT/Executor URIs` and the `IP and Port` of the MT/Executor Instances and change the array of Provers if a prover was spun up

:::

Now, we are going to put everything together in order to run the Polygon zkEVM node instance on the public testnet. Simply add the following entries to the `docker-compose.yml` file:

```yaml title="docker-compose.yml"
  zkevm-rpc:
    container_name: zkevm-rpc
    image: zkevm-node
    ports:
      - 8545:8545
    environment:
      - ZKEVM_NODE_STATEDB_HOST=zkevm-state-db
      - ZKEVM_NODE_POOL_HOST=zkevm-pool-db
      - ZKEVM_NODE_RPC_DB_HOST=zkevm-rpc-db
      - ZKEVM_NODE_RPC_BROADCASTURI=public-grpc.zkevm-test.net:61090
    volumes:
      - ./config.toml:/app/config.toml
      - ./genesis.json:/app/genesis.json
    command:
      - "/bin/sh"
      - "-c"
      - "/app/zkevm-node run --genesis /app/genesis.json --cfg /app/config.toml --components rpc"

  zkevm-sync:
    container_name: zkevm-sync
    image: zkevm-node
    environment:
      - ZKEVM_NODE_STATEDB_HOST=zkevm-state-db
    volumes:
      - ./acc.keystore:/pk/keystore
      - ./config.toml:/app/config.toml
      - ./genesis.json:/app/genesis.json
    command:
      - "/bin/sh"
      - "-c"
      - "/app/zkevm-node run --genesis /app/genesis.json --cfg /app/config.toml --components synchronizer"
```

To **run the `zkNode` instance**, go to the `zkevm-node` folder in your terminal and run the following command:

```bash
docker-compose up -d
```

## Set up Block Explorer

To get a visual access to the network and stats, we need to set up a Blockscout instance. For more details about Blockscout, check out their [documentation](https://docs.blockscout.com/).

Blockscout requires access to its own `zkNode` RPC-only instance to access the network via JSON RPC Server, and a dedicated Postgres instance in order to save its data.

Feel free to customize the environment variables to set the user, password and database for the Explore Postgres instance. Make sure to update the URL to connect to the DB in the Explorer environment variable: `DATABASE_URL`.

```yaml title="docker-compose.yml"
version: '3'

services:

    zkevm-explorer-db:
        container_name: zkevm-explorer-db
        image: postgres
        ports:
            - 5432:5432
        environment:
            - POSTGRES_USER=test_user
            - POSTGRES_PASSWORD=test_password
            - POSTGRES_DB=explorer

    zkevm-explorer:
        container_name: zkevm-explorer
        image: hermeznetwork/hermez-node-blockscout:latest
        ports:
            - 4000:4000
        environment:
            - NETWORK=POE
            - SUBNETWORK=Polygon Hermez
            - COIN=ETH
            - ETHEREUM_JSONRPC_VARIANT=geth
            - ETHEREUM_JSONRPC_HTTP_URL=http://zkevm-explorer-zknode:8124
            - DATABASE_URL=postgres://test_user:test_password@zkevm-explorer-db:5432/explorer
            - ECTO_USE_SSL=false
            - MIX_ENV=prod
            - LOGO=/images/blockscout_logo.svg
            - LOGO_FOOTER=/images/blockscout_logo.svg
        command: ["/bin/sh", "-c", "mix do ecto.create, ecto.migrate; mix phx.server"]

    zkevm-explorer-zknode:
      container_name: zkevm-explorer-zknode
      image: zkevm-node
      ports:
        - 8124:8124
      environment:
        - ZKEVM_NODE_STATEDB_HOST=zkevm-state-db
        - ZKEVM_NODE_POOL_HOST=zkevm-pool-db
        - ZKEVM_NODE_RPC_DB_HOST=zkevm-rpc-db
        - ZKEVM_NODE_RPC_PORT=8124
      volumes:
        - ./config/test.node.config.toml:/app/config.toml
        - ./config/test.genesis.config.json:/app/genesis.json
      command:
        - "/bin/sh"
        - "-c"
        - "/app/zkevm-node run --genesis /app/genesis.json --cfg /app/config.toml --components rpc --http.api eth,net,debug,zkevm,txpool,web3"
```

To **run the Block Explorer**, execute the following command:

```bash
docker-compose up -d zkevm-explorer-db
sleep 5
docker-compose up -d zkevm-explorer
sleep 5
docker-compose up -d zkevm-explorer-zknode
```

## Connecting to Metamask

:::info

Metamask requires the network to be running while configuring it, so make sure your network is up.

:::

To configure your Metamask to use your custom zkEVM network, follow these steps:

1. Log in to your Metamask wallet
2. Click on your account picture and then on **Settings**
3. On the left menu, click on **Networks**
4. Click on **Add Network** button
5. Fill up the L2 network information
    * **Network Name:** Polygon zkEVM - Goerli
    * **New RPC URL:** <http://IP-and-Port-of-zkEVM-Node-Instance>
    * **Chain ID:** 1402
    * **Currency Symbol:** ETH
    * **Block Explorer URL:** <http://IP-and-Port-of-Explorer-Instance>
6. Click on **Save** and close the Settings
7. Click on the list of networks in the top right corner
9. Select **Polygon zkEVM - Goerli**
