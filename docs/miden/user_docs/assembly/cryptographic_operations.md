---
id: cryptographic_operations
title: Cryptographic operations
sidebar_label: Cryptographic operations
description: "Instructions for performing common cryptographic operations."
keywords:
  - docs
  - matic
  - polygon
  - miden
  - cryptographic
image: https://wiki.polygon.technology/img/thumbnail/polygon-miden.png
---

Miden assembly provides a set of instructions for performing common cryptographic operations. These instructions are listed in the table below.

### Hashing and Merkle trees
[Rescue Prime](https://eprint.iacr.org/2020/1143) is the native hash function of Miden VM. The parameters of the hash function were chosen to provide 128-bit security level against preimage and collision attacks. The function operates over a state of 12 field elements, and requires 7 rounds for a single permutation. However, due to its special status within the VM, computing Rescue Prime hashes can be done very efficiently. For example, applying a permutation of the hash function can be done in a single VM cycle. 

| Instruction    | Stack_input     | Stack_output   | Notes                                      |
| -------------- | --------------- | -------------- | ------------------------------------------ |
| rpperm         | [C, B, A, ...]  | [F, E, D, ...] | $\{D, E, F\} \leftarrow permute(A, B, C)$ <br></br> where, $permute()$ computes a Rescue Prime permutation. |
| rphash         | [B, A, ...]     | [C, ...]       | $C \leftarrow hash(A,B)$ <br></br> where, $hash()$ computes a 2-to-1 Rescue Prime hash. |
| mtree_get      | [d, i, R, ...]  | [V, R, ...] | Verifies that a Merkle tree with root $R$ opens to node $V$ at depth $d$ and index $i$. Merkle tree with root $R$ must be present in the advice provider, otherwise execution fails. |
| mtree_set      | [d, i, V, R, ...] | [V, R', ...] | Updates a node in the Merkle tree with root $R$ at depth $d$ and index $i$ to value $V$. $R'$ is the Merkle root of the resulting tree. Merkle tree with root $R$ must be present in the advice provider, otherwise execution fails. At the end of the operation Merkle tree with root $R$ is removed from the advice provider. |
| mtree_cwm      | [d, i, V, R, ...] | [V, R', R, ...] | Copies a Merkle tree with root $R$ and updates a node at depth $d$ and index $i$ in the copied tree to value $V$. $R'$ is the Merkle root of the new tree. Merkle tree with root $R$ must be present in the advice provider, otherwise execution fails. At the end of the operation the advice provider will contain both Merkle trees. |
