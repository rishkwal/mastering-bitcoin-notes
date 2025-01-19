---
title: Mastering Bitcoin - Chapter 12
author: Rishabh
pubDatetime: 2024-07-04T06:30:00Z
slug: mastering-bitcoin-ch-12
featured: false
draft: true
tags:
  - Bitcoin
  - Mastering Bitcoin
description:
    Notes on the twelfth chapter of Mastering Bitcoin by Andreas M. Antonopoulos
---

## Mining and Consensus

> Mining secures the Bitcoin system and enables the emergenece of a network-wide consensus without a central authority.

The reward of newly mined bitcoins and transaction feels is an incentive to secure the network and implement monetary supply.

A block is mined every 10 minutes on average confirming the transactions present in the block.

Transactions have a topological order. One transaction is earlier than another if it appears in an earlier block or earlier in the same block. It is considred valid only if it spends an output of an earlier transaction.

