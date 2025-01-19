---
title: Mastering Bitcoin - Chapter 7
author: Rishabh
pubDatetime: 2024-07-02T12:30:00Z
slug: mastering-bitcoin-ch-07
featured: false
draft: true
tags:
  - Bitcoin
  - Mastering Bitcoin
description:
    Notes on the seventh chapter of Mastering Bitcoin by Andreas M. Antonopoulos
---

## Authorization and Authentication

When you receive bitcoin, you decide who will have the ability to spend them, this is called *authorization*. You also decide how full nodes will authenticate the transaction, this is called *authentication*.

The original version of Bitcoin introduced a programming language called *script*. It is a Forth-like stack based language.

### Turing Incomplete

Bitcoin's script is intentionally designed to be *Turing incomplete*. This means that it cannot perform all possible computations. This is done to prevent infinite loops and to make the language predictable.

Script is also stateless.

An output script specifies the conditions that must be met to spend the output. The input script provides the data and signatures required to satisfy the conditions.

### Separate execution of output and input scripts

In the original client, output and input scripts were concatenated and executed in sequence. This was changed in 2010 because of a vulnerability known as 1 OP_RETURN bug. In the current implementation, the scripts are executed seprately with the stack transferred between them.

First the input script is executed using the stack execution engine. If the input script is executed successfully, the stack is copied and the output script is executed. If the output script is executed successfully, the transaction is valid.

### Pay to Public Key Hash

Output Script:

`OP_DUP OP_HASH16 <Key Hash> OP_EQUALVERIFY OP_CHECKSIG`

Input Script:

`<Signature> <Public Key>`

### Scripted Multisignatures

These are t-of-k signatures.

Output Script:

`t <Public Key 1> <Public Key 2> ... <Public Key k> k OP_CHECKMULTISIG`

Input Script:

`0 <Signature 1> <Signature 2> ... <Signature t>`

At the time of this book being written, the maximum value of k is 3. But, this limit only applies to standard multisig scripts not to scripts wrapped in P2SH, P2WSH, or P2TR. 

P2SH is limited to 15-of-15. All other script types are limited to 20 keys per OP_CHECKMULTISIG and OP_CHECKMULTISIGVERIFY operation alothough one script may include multiple of those operations.

### Oddity in CHECKMULTISIG

It should consume t + k + 2 elements but it pops and extra item.

`<Sig B> <Sig C> 2 <Pubkey A> <Pubkey B> <Pubkey C> 3 OP_CHECKMULTISIG`

This should work but unfortunately, it doesn't. We have to provide an extra dummy element. First 3 is popped, then the three pubkeys, then 2 and then it should have popped two signatures but it has to pop 3 items due to a bug in the implementation. A workaround is to provide an extra dummy element.

`OP_0 <Sig B> <Sig C> 2 <Pubkey A> <Pubkey B> <Pubkey C> 3 OP_CHECKMULTISIG`

### Pay to Script Hash

....

## MAST(Merkelized Alternative Script Tree)

