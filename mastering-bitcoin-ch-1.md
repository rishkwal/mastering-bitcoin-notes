---
title: Mastering Bitcoin - Chapter 1
author: Rishabh
pubDatetime: 2024-07-01T06:30:00Z
slug: mastering-bitcoin-ch-1
featured: false
draft: false
tags:
  - Bitcoin
  - Mastering Bitcoin
description:
    Notes on the first chapter of Mastering Bitcoin by Andreas M. Antonopoulos
---

## Introduction

Bitcoin is a collection of concepts and technologies that form the basis of a digital money ecosystem that comprise a Bitcoin Network and a Bitcoin Protocol.

There are no physical or even digital coins. The coins are implied in transactions that transfer value from spender to receiver.

Bitcoin is a distributed peer to peer system implying there is no central server or point of control. Units of Bitcoin are created through a process called mining.

Bitcoin builds on decades of research in cryptography and distributed systems and includes at least four key innovations brought together in a unique and powerful combination. It consists of:

- A decentralized peer-to-peer network (The Bitcoin Protocol)
- A public transaction journal(the Blockchain)
- A set of rules for indedepndent transaction validation and currency issuance (Consensus Rules)
- A mechanism for reaching global decentralized consensus on the valid blockchain (Proof of Work Algorithm)

Bitcoin uses cryptography to solve the [double-spending problem](https://en.wikipedia.org/wiki/Double-spending): the possibility of a single entity spending the same amount multiple times. This is done by using a blockchain which is a public ledger of all transactions that have ever been executed.

## History of Bitcoin

The first description of Bitcoin was published in 2008 by an entity named Satoshi Nakamoto in a paper titled ["Bitcoin: A Peer-to-Peer Electronic Cash System"](https://bitcoin.org/bitcoin.pdf). The first block of the Bitcoin blockchain, called the Genesis Block, was mined in January 2009.

Nakamoto withdrew from the public in April 2011 leaving the responsibility of the Bitcoin software to a group of volunteers.

Satoshi found a novel soultion to ["Byzantine Generals' Problem"](https://en.wikipedia.org/wiki/Byzantine_fault) which is a problem in distributed computing and game theory. The problem is how to achieve consensus among a group of entities that don't trust each other.

## Wallets

To get started with Bitcoin, you need to install a Bitcoin wallet on your computer or mobile device. A wallet is a software that holds the keys necessary to spend Bitcoin. There are several types of wallets available:

### Desktop and Mobile Wallets

These wallets are installed on your computer or mobile device. They give you full control over your Bitcoin. Some popular wallets are:

### Web Wallet

They are accessed through a web browser and store your wallet on a serer owned by a third party.

### Hardware Wallet

They are physical devices that store your Bitcoin offline. They are considered the most secure way to store Bitcoin.

**Full Node vs Lightweight**

### Full Node

A full node is a program that fully validates transactions and blocks. It is the most secure way to use Bitcoin.

### Lightweight client

It is also known as simplified-payment-verification (SPV) client. It partially validates transactions it receives and independently creates new transactions.

### Third Party APIs

The Bitcoin wallet trusts a third party to provide it with accurate information and provide privacy.

## Keys

Access to Bitcoin is controlled by ["private keys"](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch04_keys.adoc#private-keys). If you are the only one to have control over these privatre keys, you have control over the bitcoins. If you don't control the private keys, you don't control the bitcoins. Andreas coined the phrase: *Your keys, your coins. Not your keys, not your coins.*

### Recovery Codes

Recovery codes are used to recover your wallet in case you lose your private keys. They are a list of words that can be used to recover your wallet. They are also known as "mnemonic codes" or "seed phrases".

Example of a recovery code:

```java
witch collapse practice feed shame open despair creek road again ice least
```

## Addresses

When you want to receive Bitcoin, you need to provide the sender with a Bitcoin address. A bitcoin address is simply a number that represents a possible destination for a bitcoin payment. It is a string of alphanumeric characters, but can also be represented as a scannable QR code.

## Transactions

Transactions in Bitcoin are irreversible. Transactions can be done by sending bitcoins from one address to another. There is a transaction fee associated with each transaction. The higher the fee, the faster the transaction is processed(not exactly).

### Confirmation

A transaction is considered confirmed when it is included in a block on the network transactiona journal(blockchain). To be confirmed, a transaction must be included in a block on the blockchain. The number of confirmations is the number of blocks that have been added after the block that includes your transaction.

### **Read chapter 2 Notes**: [Mastering Bitcoin - Chapter 2](/posts/mastering-bitcoin-ch-2)
