---
title: Mastering Bitcoin - Chapter 5
author: Rishabh
pubDatetime: 2024-07-02T12:30:00Z
slug: mastering-bitcoin-ch-05
featured: false
draft: true
tags:
  - Bitcoin
  - Mastering Bitcoin
description:
    Notes on the fifth chapter of Mastering Bitcoin by Andreas M. Antonopoulos
---

## Wallet Recovery

Losing access to a private key can make it impossible to spend the funds associated with it. This chapter examines the various ways to recover a wallet.

Wallets don't contain bitcoins, they contain keys. The keys allow the owner to spend the bitcoins.

Simple wallet databases store both private keys and addresses to which bitcoins are received.

It is possible for a wallet to independently genrate each of the wallet keys it later plans to use. This is called non deteministic generation, For each key the user would need to back up about 32 bytes, plus overhead.

### Deterministic Key Generation

