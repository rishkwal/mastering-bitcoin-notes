---
title: Mastering Bitcoin - Chapter 2
author: Rishabh
pubDatetime: 2024-07-01T08:30:00Z
slug: mastering-bitcoin-ch-02
featured: false
draft: false
tags:
  - Bitcoin
  - Mastering Bitcoin
description:
    Notes on the second chapter of Mastering Bitcoin by Andreas M. Antonopoulos
---

## How Bitcoin Works

The Bitcoin system does not require trust in third parties. It is decentralized and each user can verify the operations of the network.

The system consists of users with wallets, transactions, miners, blocks and the blockchain.

You can look at a blockchain explorer to get an overview of the Bitcoin network. My favorite is [mempool.space](https://mempool.space).

### Invoice

An invoice is a request for payment. It is a message that includes the amount to be paid and the address to which the payment should be sent.

Example of an invoice:

![Invoice QR](../../assets/images/invoice.png)

This invoice encodes the following information:

```plaintext
bitcoin:bc1qk2g6u8p4qm2s2lh3gts5cpt2mrv5skcuu7u3e4?amount=0.01577764&
label=Bob%27s%20Store&
message=Purchase%20at%20Bob%27s%20Store

Components of the URI

A Bitcoin address: "bc1qk2g6u8p4qm2s2lh3gts5cpt2mrv5skcuu7u3e4"
The payment amount: "0.01577764"
A label for the recipient address: "Bob's Store"
A description for the payment: "Purchase at Bob's Store"
```

## Transactions

### Inputs and Outputs

Transactions are like lines in a double-entry bookkeeping ledger. Each transaction contains one or more inputs which spend funds and one or more outputs which receive funds. The sum of the inputs must be greater than or equal to the sum of the outputs due to fees.

![Transaction](https://github.com/bitcoinbook/bitcoinbook/raw/develop/images/mbc3_0202.png)

### Transaction Chains

Each input in a transaction must refer to a previous output. This creates a chain of transactions that can be traced back to the coinbase transaction of a block.

Transactions don't include the value of the inputs. The value is calculated by referencing the previous transaction outputs.

**Satoshi(sats)**

The protocol uses a unit called a satoshi which is the smallest unit of Bitcoin. 1 BTC = 100,000,000 satoshis.

### Change

Transactions can also include a change that is sent back to the sender. This is done by creating a new output that sends the remaining funds back to the sender. The change address does not need to be the same as the sender's address. Transactions not having a change are called **changeless transactions**.

### Coin Selection

When creating a transaction, the wallet selects the inputs to spend. Similar to how you spend cash, you select the bills and coins that add up to the amount you want to spend. If you don't have the exact amount, you select the closest amount and get change back.

In Bitcoin you also have to consider the fees. The wallet will try to select the inputs that minimize the fees.

### Common Transaction Types

1. Common transactions - A transaction with one input and two outputs.
2. Aggregation transactions - A transaction with multiple inputs and one output.
3. Distributing transactions - A transaction with one input and multiple outputs.

### Constructing a Transaction

Wallet contains all the logic, user only needs to provide the amount, fee and the recipient's address.

#### Getting the right inputs

A wallet that runs on a full node contains the entire UTXO set. It can select the right inputs to spend.

> An UTXO or Unspent Transaction Output is a transaction output that has not been spent yet. It is a record of the amount of bitcoin that can be spent.

#### Creating the outputs

A transaction output is created with a script that says something like, "This output is paid to whoever can present a signature from the key corresponding to receiver’s public address." Since only the reciever has the key, only they can spend the output.

The transaction also includes a change output.

Along with the outputs, the transaction includes a fee that is paid to the miner. It is the difference between the sum of the inputs and the sum of the outputs.

#### Adding transaction to the blockchain

The transaction is broadcasted to the network. It is propagated by the nodes and eventually included in a block by a miner.

The transaction doesn't necessarily need to be propagated to the receiver first. It can be sent to any node in the network that can propagate it to the rest of the network.

## Bitcoin Mining

A transaction is not considered final until it is included in a block. This is done by miners who compete to solve a cryptographic puzzle. The first miner to solve the puzzle gets to add the block to the blockchain. The block has to be validated by the rest of the network.

Mining uses a lot of computational power and electricity. Miners are rewarded with a block reward and transaction fees. The block reward is halved every 210,000 blocks. The more power that is added to the network, the harder the puzzle becomes and more secure the network becomes.

The miner have to perform something called hashing on the block to come up with a hash that is less than a certain target. Once the miner finds the hash, they broadcast the block to the network. The other nodes validate the block and if it is valid, they add it to their copy of the blockchain.

This mechanism is called ***Proof of Work***. It is a way to reach consensus in a decentralized network.

Transactions are added to the block based on the fees. The miner can choose which transactions to include in the block.

Every block that is mined contains a reference to the previous block. This creates a chain of blocks that is immutable. This is known as the blockchain.

### **Read chapter 3 Notes**: [Mastering Bitcoin - Chapter 3](/posts/mastering-bitcoin-ch-3)