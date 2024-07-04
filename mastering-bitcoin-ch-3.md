---
title: Mastering Bitcoin - Chapter 3
author: Rishabh
pubDatetime: 2024-07-02T08:30:00Z
slug: mastering-bitcoin-ch-03
featured: false
draft: false
tags:
  - Bitcoin
  - Mastering Bitcoin
description:
    Notes on the third chapter of Mastering Bitcoin by Andreas M. Antonopoulos
---

Chapter 3 talks about the reference implementation of Bitcoin, ["Bitcoin Core"](https://github.com/bitcoin/bitcoin).

The Bitcoin system was designed so that it is possible to run a software locally to prevent counterfeiting, debasement and serveral other critical problems. This software is called a *full verification node* because it verifies every confirmed Bitcoin transaction.

Bitcoin Core is known as the reference implementation because it defines the Bitcoin protocol and provides a reference for how each part of the protocol should be implemented. Other implementations use Bitcoin Core as a reference to ensure compatibility with the network.

![Bitcoin Core Architecture](https://github.com/bitcoinbook/bitcoinbook/raw/develop/images/mbc3_0301.png)

Most major parts of the system since 2011 have been documented in the [Bitcoin Improvement Proposals (BIPs)](https://github.com/bitcoin/bips).

## Compiling and running Bitcoin from Source Code

No notes. Follow [this tutorial](https://jonatack.github.io/articles/how-to-compile-bitcoin-core-and-run-the-tests)

## Bitcoin Core Nodes

The peer-to-peer network is composed of network "nodes", run mostly by individuals and some businesses. Running a node requires downloading and processing over 500 GB of data initially and about 400 MB of Bitcoin transactions per day(as of 2023). If you close Bitcoin Core for 10 days, you will need to download 4 GB of data to catch up.

**If your internet connection is limited, has a low data cap, or is metered, running a full node may not be the best choice.**

### Why Run a node?

- You don't want to rely on a third party to validate the transactions you receive
- You don't want to disclose the wallets that belong to you
- You are developing Bitcoin Software
- Building applications on Bitcoin
- You want to support the network

### Configuring the node

Bitcoin Core can be configured using the `bitcoin.conf` file. The file is located in the Bitcoin data directory. The file can be located by running `bitcoind -printtoconsole` and looking for the `bitcoin.conf` file. Then hit `Ctrl+C` to stop the process.

#### Configuration Options

`alertnotify`: Execute command when a relevant alert is received or we see a really long fork (%s in cmd is replaced by message)

`datadir`: Specify data directory

`prune`: Reduce storage requirements by enabling pruning (deleting) of old blocks. This allows the pruneblockchain RPC to be called to delete specific blocks, and enables automatic pruning of old blocks if a target size in MiB is provided.

`txindex`: Maintain a full transaction index, used by the getrawtransaction rpc call (default: 0)

`dbcache`: Set database cache size in megabytes (default: 450)

`blocksonly`: Do not accept transactions from remote peers.

`maxmempool`: Keep the transaction memory pool below megabytes

### Commands

- `bitcoin-cli getblockhash 1000`: Get the hash of the block at height 1000
- `bitcoin-cli getnetworkinfo`: Get network information
- `bitcoin-cli getrawtransaction <txid>`: Get raw transaction
- `bitcoin-cli decoderawtransaction <hex>`: Decode raw transaction
- `bitcoin-cli getblock <hash>`: Get block information

#### Decoding a block

```
{
  "hash": "0000000000002917ed80650c6174aac8dfc46f5fe36480aaef682ff6cd83c3ca",
  "confirmations": 651742,
  "height": 123456,
  "version": 1,
  "versionHex": "00000001",
  "merkleroot": "0e60651a9934e8f0decd1c[...]48fca0cd1c84a21ddfde95033762d86c",
  "time": 1305200806,
  "mediantime": 1305197900,
  "nonce": 2436437219,
  "bits": "1a6a93b3",
  "difficulty": 157416.4018436489,
  "chainwork": "[...]00000000000000000000000000000000000000541788211ac227bc",
  "nTx": 13,
  "previousblockhash": "[...]60bc96a44724fd72daf9b92cf8ad00510b5224c6253ac40095",
  "nextblockhash": "[...]00129f5f02be247070bf7334d3753e4ddee502780c2acaecec6d66",
  "strippedsize": 4179,
  "size": 4179,
  "weight": 16716,
  "tx": [
    "5b75086dafeede555fc8f9a810d8b10df57c46f9f176ccc3dd8d2fa20edd685b",
    "e3d0425ab346dd5b76f44c222a4bb5d16640a4247050ef82462ab17e229c83b4",
    "137d247eca8b99dee58e1e9232014183a5c5a9e338001a0109df32794cdcc92e",
    "5fd167f7b8c417e59106ef5acfe181b09d71b8353a61a55a2f01aa266af5412d",
    "60925f1948b71f429d514ead7ae7391e0edf965bf5a60331398dae24c6964774",
    "d4d5fc1529487527e9873256934dfb1e4cdcb39f4c0509577ca19bfad6c5d28f",
    "7b29d65e5018c56a33652085dbb13f2df39a1a9942bfe1f7e78e97919a6bdea2",
    "0b89e120efd0a4674c127a76ff5f7590ca304e6a064fbc51adffbd7ce3a3deef",
    "603f2044da9656084174cfb5812feaf510f862d3addcf70cacce3dc55dab446e",
    "9a4ed892b43a4df916a7a1213b78e83cd83f5695f635d535c94b2b65ffb144d3",
    "dda726e3dad9504dce5098dfab5064ecd4a7650bfe854bb2606da3152b60e427",
    "e46ea8b4d68719b65ead930f07f1f3804cb3701014f8e6d76c4bdbc390893b94",
    "864a102aeedf53dd9b2baab4eeb898c5083fde6141113e0606b664c41fe15e1f"
  ]
}
```

`confirmations`: The number of blocks in the longest block chain that link back to this block

`height`: The height of the block in the block chain

`version`: The block version

`versionHex`: The block version in hexadecimal

`merkleroot`: The Merkle root of the block

`time`: The time the block was mined(accoring to the miner)

`mediantime`: The median time of the 11 blocks before this block

... etc

### Programmatic Interface

Bitcoin Core provides a JSON-RPC interface that allows you to interact with the node programmatically.

A password is created and stored in the data directory under the name `.cookie`.

The request can look like:

```bash
$ curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest",
  "method": "getblockchaininfo",
  "params": [] }' -H 'content-type: text/plain;' http://127.0.0.1:8332/
```

OR

```bash
$ curl
  --user __cookie__:17c9b71cef21b893e1a019f4bc071950c7942f49796ed061b274031b17b19cd0
  --data-binary '{"jsonrpc": "1.0", "id":"curltest",
  "method": "getblockchaininfo",
  "params": [] }' -H 'content-type: text/plain;' http://127.0.0.1:8332/
```

Most languages have libraries that can interact with the JSON-RPC interface.
