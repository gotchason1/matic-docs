---
id: protocol
title: Nightfall Protocol
sidebar_label: Nightfall Protocol
description: The Nightfall Protocol.
keywords:
  - docs
  - polygon
  - nightfall
  - protocol
  - chain
  - block
  - structure
  - layer
image: https://wiki.polygon.technology/img/thumbnail/polygon-nightfall.png
---

We assume that at least 1 Proposer has registered with the system, posting a minimum Stake.

## **Transaction Posting**

There are two mechanisms to post new transactions:

- [On-chain](#on-chain)
- [Off-chain](#off-chain)

### **On-chain**

The process starts with a Transactor creating a transaction by calling `submitTransaction` on `Shield.sol`. The Transactor pays a fee to the Shield contract for the Transaction, which can be anything the Transactor decides. This will be paid to the Proposer that incorporates the Transaction in a Block. Currently, the proposer and the underlying Optimist instance will likely choose the higher fees to incorporate in a block, pretty much like a miner would do.

The Transaction call causes a blockchain event to be posted, containing its details. If it is a Deposit, the Shield contract takes payment of the Layer 1 ERC token in question.

### **Off-chain**

Transfer and Withdraw transactions have the option of being submitted directly to listening proposers rather than being submitted on-chain via the above process.

These off-chain transactions will save Transactors the on-chain submission cost of a deposit (~45k gas), but they require a greater degree of trust between transactors and the proposers they choose to connect to. It is easier for bad acting proposers to censor transactions received off-chain than those received on-chain, as these transactions are not broadcasted to all listening proposers. In this case, transactors should only consider a transaction trustable when the cooling-off period (1 week) is passed.

## **Transaction Acceptance**

When Proposers receive any transactions, they perform a range of checks to validate that the transaction is well-formed and that the proof verifies against the public input hash.

If all the checks pass, the transaction is added to Proposer's mempool to be considered in a Block.

## **Block Assembly and Submission**

Proposers wait until the Shield contract assigns them as the current proposer.

The current Proposer receives, from its own internal Optimist instance, a new block containing transactions from its mempool. For each transaction, it will compute the new commitment Merkle Tree that would come into being if these transaction were to be added to the Shield contract.

Therefore, the Block contains hashes of the Transactions included in the Block and the commitment Merkle Tree root as it would exist after processing all the transactions in the Block (Commitment Root). Then the Proposer sends this block to the State smart contract.

When a block is proposed, the following information is recorded on-chain:

- Block data structure
```solidity
  struct Block {
    uint48 leafCount; // note this is defined to be the number of leaves BEFORE the commitments in this block are added
    address proposer;
    bytes32 root; // the 'output' commitment root after adding all commitments
    uint256 blockNumberL2;
    bytes32 previousBlockHash;
    bytes32 transactionHashesRoot;
  }
```
- Transactions in block
```solidity
    struct Transaction {
        uint112 value;
        uint112 fee;
        TransactionTypes transactionType;
        TokenType tokenType;
        uint64[2] historicRootBlockNumberL2; // number of L2 block containing historic root
        uint64[2] historicRootBlockNumberL2Fee; //number of L2 block containing historic root fee
        bytes32 tokenId;
        bytes32 ercAddress;
        bytes32 recipientAddress;
        bytes32[2] commitments;
        bytes32[2] nullifiers;
        bytes32[1] commitmentFee;
        bytes32[2] nullifiersFee;
        bytes32[2] compressedSecrets;
        uint256[4] proof;
    }
``` 

## **Challenges**

The blocks will be challengeable in the queue for a week, during which their correctness may be challenged by calling one of the challenging functions. The challenges that can be made are:

- **INVALID_PROOF** - the proof given in a transaction does not verify as true;
- **INVALID_PUBLIC_INPUT_HASH** - the public input hash of a transaction is not the correct hash of the public inputs;
- **HISTORIC_ROOT_EXISTS** - the root of the commitment Merkle Tree used to create the transaction proof has never existed;
- **DUPLICATE_NULLIFIER** - a nullifier, given as part of a Transaction, is present in the list of spent nullifiers;
- **HISTORIC_ROOT_INVALID** - the updated commitment root that results from processing the transactions in the Block is not correct;
- **DUPLICATE_COMMITMENT** - an identical transaction included in this block has already been included in a prior block;
- **TRANSACTION_TYPE_INVALID** - the transaction is not well-formed based on the transaction type (e.g. Deposit, Transfer, Withdrawal).

Should the challenge succeed, i.e. the on-chain computation shows it to be a valid challenge, then the following actions are taken:

- The hash of the Block in question and all subsequent Blocks are removed from the queue.
- The Block stake, submitted by the Proposer, is paid to the Challenger.
- The Transactors with a Transaction in the Block are reimbursed with the fee that they would have paid to the Proposer and any escrowed funds held by the Shield contract in case of a Deposit transaction.
