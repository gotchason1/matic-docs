---
id: full-node-deployment
title: Run a full node with Ansible
description: Deploy a Full Node using Ansible
keywords:
  - docs
  - polygon
  - matic
  - node
  - full node setup
  - ansible
  - deploy
image: https://wiki.polygon.technology/img/polygon-wiki.png
---

import useBaseUrl from '@docusaurus/useBaseUrl';

This tutorial guides you through starting and running a full node using Ansible. 

An [Ansible playbook](https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html) is used to 
configure and manage a full node. See the [Minimum Technical Requirements](technical-requirements.md) guide for the system requirements.

:::tip

Steps in this guide involve waiting for the Heimdall and Bor services to sync fully. This process takes several days to complete.

Alternatively, you can use a maintained snapshot, reducing the sync time to a few hours. For detailed instructions, see [<ins>Snapshot Instructions for Heimdall and Bor</ins>](/docs/develop/network-details/snapshot-instructions-heimdall-bor).

For snapshot download links, see the [<ins>Polygon Chains Snapshots</ins>](https://snapshots.matic.today/) page.

:::

### Prerequisites

- Install Ansible on your local machine with Python3.x. The setup will not work if you have Python2.x.
    - To install Ansible with Python 3.x, you can use pip. If you do not have pip on your machine, 
      follow the steps outlined [here](https://pip.pypa.io/en/stable/). Run `pip3 install ansible` to install 
      Ansible.
- Check the [Polygon PoS Ansible repository](https://github.com/maticnetwork/node-ansible#requirements) for 
  requirements.
- You will also need to ensure that Go is **not installed** in your environment. You will run into issues if you attempt to set up your full node through Ansible with Go installed as Ansible requires specific packages of Go to be installed.
- You will also need to make sure that your VM / Machine does not have any previous setups for Polygon Validator or Heimdall or Bor. You will need to delete them as your setup will run into issues.

:::info Heimdall source enhancements

The latest Heimdall version, **[v.0.2.12](https://github.com/maticnetwork/heimdall/releases/tag/v0.2.12)**, contains a few enhancements. 
The delay time between the contract events of different validators **has been increased** to ensure that the mempool doesn't get filled 
quickly in case of a burst of events that could hamper the chain's progress. 

Additionally, the data size **has been restricted in state sync txs to 30Kb (when represented in bytes) and 60Kb (when defined as string)**. 
For example:

```bash
Data - "abcd1234"
Length in string format - 8
Hex Byte representation - [171 205 18 52]
Length in byte format - 4
```
:::

## Full node setup

- Ensure you have access to the remote machine or VM on which the full node is being set up. 
  > Refer to [https://github.com/maticnetwork/node-ansible#setup](https://github.com/maticnetwork/node-ansible#setup) for more details.
- Clone the [https://github.com/maticnetwork/node-ansible](https://github.com/maticnetwork/node-ansible) repository.
- Navigate into the node-ansible folder: `cd node-ansible`
- Edit the `inventory.yml` file and insert your IP(s) in the `sentry->hosts` section. 
  > Refer to [https://github.com/maticnetwork/node-ansible#inventory](https://github.com/maticnetwork/node-ansible#inventory) for more details.
- Check if the remote machine is reachable by running: `ansible sentry -m ping`
- To test if the correct machine is configured, run the following command:

  ```bash
  # Mainnet:
  ansible-playbook -l sentry playbooks/network.yml --extra-var="bor_branch=v0.2.16 heimdall_branch=v0.2.12 network_version=mainnet-v1 node_type=sentry/sentry heimdall_network=mainnet" --list-hosts

  # Testnet:
  ansible-playbook -l sentry playbooks/network.yml --extra-var="bor_branch=v0.3.0 heimdall_branch=v0.3.0 network_version=testnet-v4 node_type=sentry/sentry heimdall_network=mumbai" --list-hosts
  ```

  <img src={useBaseUrl("img/network/full-node-mumbai.png")} />

- Next, set up the full node with this command:

  ```bash
  # Mainnet:
  ansible-playbook -l sentry playbooks/network.yml --extra-var="bor_branch=v0.2.16 heimdall_branch=v0.2.12 network_version=mainnet-v1 node_type=sentry/sentry heimdall_network=mainnet"

  # Testnet:
  ansible-playbook -l sentry playbooks/network.yml --extra-var="bor_branch=v0.3.0 heimdall_branch=v0.3.0 network_version=testnet-v4 node_type=sentry/sentry heimdall_network=mumbai"
  ```

- In case you run into any issues, delete and clean the whole setup using:
  ```
  ansible-playbook -l sentry playbooks/clean.yml
  ```

- Once you initiate the Ansible playbook, log in to the remote machine.
- Configure the following in `~/.heimdalld/config/config.toml`:
  ```bash
  moniker=<enter unique identifier>

  # Mainnet:
  seeds="f4f605d60b8ffaaf15240564e58a81103510631c@159.203.9.164:26656,4fb1bc820088764a564d4f66bba1963d47d82329@44.232.55.71:26656,2eadba4be3ce47ac8db0a3538cb923b57b41c927@35.199.4.13:26656,3b23b20017a6f348d329c102ddc0088f0a10a444@35.221.13.28:26656,25f5f65a09c56e9f1d2d90618aa70cd358aa68da@35.230.116.151:26656"

  # Testnet:
  seeds="4cd60c1d76e44b05f7dfd8bab3f447b119e87042@54.147.31.250:26656"
  ```
- Configure the following in `~/.heimdalld/config/heimdall-config.toml`:
    - `eth_rpc_url =<insert Infura or any full node RPC URL to Goerli>`
- Add the following flag in `~/node/bor/start.sh` to the `bor` start params:

  ```bash
  # Mainnet:
  --bootnodes "enode://0cb82b395094ee4a2915e9714894627de9ed8498fb881cec6db7c65e8b9a5bd7f2f25cc84e71e89d0947e51c76e85d0847de848c7782b13c0255247a6758178c@44.232.55.71:30303,enode://88116f4295f5a31538ae409e4d44ad40d22e44ee9342869e7d68bdec55b0f83c1530355ce8b41fbec0928a7d75a5745d528450d30aec92066ab6ba1ee351d710@159.203.9.164:30303"
  
  # Testnet:
  --bootnodes "enode://320553cda00dfc003f499a3ce9598029f364fbb3ed1222fdc20a94d97dcc4d8ba0cd0bfa996579dcc6d17a534741fb0a5da303a90579431259150de66b597251@54.147.31.250:30303"
  ```

- In case you want to turn `trace` on for Bor, add the following flag to the `bor` start params in `~/node/bor/start.sh`:
    - `--gcmode 'archive'`

- Run the full node with the following commands:
    - `sudo service heimdalld start`
    - `sudo service heimdalld-rest-server start`

- To check if Heimdall is synced
    - On the remote machine/VM, run `curl localhost:26657/status`
    - In the output, `catching_up` value should be `false`

- Once Heimdall is synced, run
    - `sudo service bor start`

You have successfully set up a full node with Ansible.

## Logs

Logs can be managed by the `journalctl` linux tool. Here is a tutorial for advanced usage: [How To Use Journalctl to View and Manipulate Systemd Logs](https://www.digitalocean.com/community/tutorials/how-to-use-journalctl-to-view-and-manipulate-systemd-logs).

**Check Heimdall node logs**

```bash
journalctl -u heimdalld.service -f
```

**Check Heimdall Rest-server logs**

```bash
journalctl -u heimdalld-rest-server.service -f
```

**Check Bor Rest-server logs**

```bash
journalctl -u bor.service -f
```

## Ports and Firewall Setup

Open ports 22, 26656 and 30303 to world (0.0.0.0/0) on sentry node firewall.

You can use VPN to restrict access for port 22 as per your requirement and security guidelines.