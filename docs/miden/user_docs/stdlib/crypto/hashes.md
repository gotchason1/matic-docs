---
id: hashes
title: Cryptographic hashes
sidebar_label: Cryptographic hashes (std::crypto::hashes)
description: "Cryptographic hash functions."
keywords:
  - docs
  - matic
  - polygon
  - miden
  - hash
  - BLAKE3
  - SHA256
image: https://wiki.polygon.technology/img/thumbnail/polygon-miden.png
---

Namespace `std::crypto` contains modules for commonly used cryptographic hash functions.

## BLAKE3
Module `std::crypto::hashes::blake3` contains procedures for computing hashes using [BLAKE3](https://blake3.io/) hash function. The input and output elements are assumed to contain one 32-bit value per element.

| Procedure | Description |
| ----------- | ------------- |
| hash | Computes BLAKE3 2-to-1 hash.<br/><br/>Input: 64-bytes stored in the first 16 elements of the stack (32 bits per element).<br /> <br/>Output: A 32-byte digest stored in the first 8 elements of stack (32 bits per element) with the remaining 8 elements of stack set to zeros. |

## SHA256
Module `std::crypto::hashes::sha256` contains procedures for computing hashes using [SHA256](https://en.wikipedia.org/wiki/SHA-2) hash function. The input and output elements are assumed to contain one 32-bit value per element.

| Procedure | Description |
| ----------- | ------------- |
| hash | Computes SHA256 2-to-1 hash.<br/><br/>Input: 64-bytes stored in the first 16 elements of the stack (32 bits per element).<br /> <br/>Output: A 32-byte digest stored in the first 8 elements of stack (32 bits per element) with the remaining 8 elements of stack set to zeros. |
