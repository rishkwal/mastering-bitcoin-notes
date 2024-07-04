---
title: Mastering Bitcoin - Chapter 6
author: Rishabh
pubDatetime: 2024-07-02T12:30:00Z
slug: mastering-bitcoin-ch-06
featured: false
draft: false 
tags:
  - Bitcoin
  - Mastering Bitcoin
description:
    Notes on the sixth chapter of Mastering Bitcoin by Andreas M. Antonopoulos
---

## Transactions

The way we transfer physical cash has little resemblance with how we transfer bitcoins. When we transfer physical cash, we hand over the cash to the recipient. When we transfer bitcoins, we don't actually transfer anything. Instead, we update a public ledger/database.

The data that a sender needs to provide to the network to transfer bitcoins is called a **transaction**.

## Decoding a serialized transaction

```bash
$ bitcoin-cli getrawtransaction 466200308696215bbc949d5141a49a41\
38ecdfdfaa2a8029c1f9bcecd1f96177

01000000000101eb3ae38f27191aa5f3850dc9cad00492b88b72404f9da13569
8679268041c54a0100000000ffffffff02204e0000000000002251203b41daba
4c9ace578369740f15e5ec880c28279ee7f51b07dca69c7061e07068f8240100
000000001600147752c165ea7be772b2c0acb7f4d6047ae6f4768e0141cf5efe
2d8ef13ed0af21d4f4cb82422d6252d70324f6f4576b727b7d918e521c00b51b
e739df2f899c49dc267c0ad280aca6dab0d2fa2b42a45182fc83e81713010000
0000
```

![Byte Map of a transaction](https://github.com/bitcoinbook/bitcoinbook/raw/develop/images/mbc3_0601.png)

### Version

The first four butes of a serialized Bitcoin transaction is the **version** field. It is used to keep track of the software version that created the transaction.

Original version was 1(0x01000000). Version 2 was introduced in [BIP68](https://github.com/bitcoin/bips/blob/master/bip-0068.mediawiki) that placed additional constraints on the sequence field. The constraints were not applicable to version 1 transactions.

BIP 112, which was part of the same soft fork, introduced an OP_CHECKSEQUENCEVERIFY(repurposed OP_NOP3) opcode which would now fail if used in a version 1 transaction.

#### Protecting Presigned Transactions

It's possible to sign a transaction without broadcasting it immediately. A presigned transaction can be saved for months or years. In the interim, the signer might lose access to the private key. A change to the protocol might invalidate the transaction. This problem is solved by reserving some transaction features fo upgrades, such as version numbers.

If you implement a protocol that uses presigned transactions, ensure that it doesn't use any features that are reserved for future upgrades. Bitcoin Core doesn't allow the usage of reserved features by default. You can check if the transaction complies with the rules by using the `testmempoolaccept` RPC.

>PSBT(Partially Signed Bitcoin Transaction ):  
>PSBT allows an untrusted party to participate in the transaction signing process. The untrusted party can create a PSBT and send it to the trusted party for signing. The trusted party can then sign the PSBT and send it back to the untrusted party for finalizing the transaction.

### Extended Marker and Flag

These two fields were added as a part of SegWit([BIP141](https://github.com/bitcoin/bips/blob/master/bip-0141.mediawiki) & [BIP143](https://github.com/bitcoin/bips/blob/master/bip-0143.mediawiki)). The extended serialization format is defined in [BIP144](https://github.com/bitcoin/bips/blob/master/bip-0144.mediawiki).

If the transaction includes a witness structure, the marker must be zero(0x00) and the flag non-zero. In current protocol the flag should always be one(0x01). If there is no witness structure, both the marker and flag must be zero(legacy serialization).

In legacy serialization, the marker byte would have been the number of inputs.

### Inputs

![Transaction Input](https://github.com/bitcoinbook/bitcoinbook/raw/develop/images/mbc3_0602.png)

The inputs field contains serveral other fields.

#### Length of Transaction Input List

This is the first field, an integer that indicates the number of inputs. Minimum value is one but there is no explicit maximum value. Encoded as a [compactSize Unsigned Integer](https://btcinformation.org/en/developer-reference#compactsize-unsigned-integers).

Each input must contain: an outpoint, an input-script and a sequence number.

#### Outpoint

It is a reference to the unspent output. Each input contains a single outpoint. It contains a 32-byte transaction output hash and a 4-byte output index number that identifies the specific output from the transaction.

> #### Question 
>
> Why don't we use CompactSize Unsigned Integer for the output index number?

The outpoint is processed by checking the amount, authorization conditions, the hegiht and median time past of the block that contains the transaction and proof that no other transaction has spent it.

Every node keeps a database of UTXO and essential metadata. Each time a block of transaction arrives, all otuputs that are spent are removed from the UTXO set and new ones added.

#### Internal and Display byte order

The *digests*(hash identifiers) in Bitcoin are displayed in big-endian order(conventional). The internal byte order is little-endian(reversed). The outpoint is displayed in internal byte order but the transaction hash is displayed in big-endian order.

Developers need to reverse the order of bytes in transaction and block identifiers when working with Bitcoin.

#### Input Script

It is a legacy transaction format. So the length prefix of the input script is set to zero.

#### Sequence Number

It was originally intended to allow for transaction replacement. It tracked the version of the transaction. The way it worked was that you could replace a transaction having a lower sequence number with a transaction having higher sequence number. It created the possibility of creating *payment channels*(high frequency transactions). But it had a lot of problems and was disabled by default.

##### RBF(Replace-By-Fee)

RBF is a way to replace a transaction with a higher fee by creating a new transaction with the same inputs but a higher fee. According to BIP125, a transaction having a sequence number less than 0xFFFFFFFE is considered replaceable.

Sure, here are very short descriptions of both types of RBF transactions:

###### **Opt-in RBF**

A Bitcoin transaction that explicitly signals it can be replaced by a higher-fee transaction through specific sequence numbers in its inputs.

###### **Full RBF**

A policy where any unconfirmed Bitcoin transaction can be replaced by a higher-fee transaction, regardless of whether it initially signaled replaceability.

##### Locktime

Inputs with sequence less than 2^<sup>31</sup> are interpreted as having a relative locktime. Such a transaction an be included in the block once the referenced output has aged the relative timelock amount(specified either in blocks or seconds). 

A type flag is used to indicate the type of locktime stored in the 23rd least significant bit. If type flag is set, sequence value is interpreted as a multiple of 512 seconds. If not, it is interpreted as number of blocks. 

Once the flags(23 and 32) are evaluated, the sequence value is masked with a 16 bit mask to get the actual value. So the max timelock value from 16 bits is a bit more than a year.

![BIP 68 definition](https://github.com/bitcoinbook/bitcoinbook/raw/develop/images/mbc3_0603.png)

Any transaction that sets relative timelock automatically signals that it is replaceable.

### Outputs

The output contains serveral fields as well.

![Byte Map of output](https://github.com/bitcoinbook/bitcoinbook/raw/develop/images/mbc3_0604.png)

#### Ouptut Count

It's a compactSize integer and must be greater than zero.

#### Amount

It is the first field of a specific output. It is an 8-byte signed integer indicating the number of satoshis to be transferred. 0 < output < 21 million.

**Uneconomical outputs and disallowed dust**

Outputs that are very small are considered dust. They are uneconomical to spend because the transaction fee would be higher than the output value. Many programs assume the minimum output value is 546 satoshis(as of 2024).

There is no incentive for person to spend an uneconomical output so it might become a burden for full node operators. The policies against relaying these dust outputs are called [*dust policies*](https://github.com/bitcoin/bitcoin/blob/3714692644f45808a6480525abc36870aeee1de4/src/policy/policy.cpp#L28).

> #### Utreexo
>
> It is a project that allows full nodes to store a commitment to the set of UTXOs rather than the data itself. A minimal commitment might only be a KB or two in size compared to over 5GB of UTXO data.

**Exception**: Output scripts starting with OP_RETURN. They are called *data carrier outputs* and can have a value of zero. These outputs can never be spent hence full nodes don't have to keep track of them.

#### Output Scripts

The amount is followed by a compactSize integer indicating the length of the output script. Minimum size is zero. There's no explicit maximum size but the script must be less than 10,000 bytes or else a later transaction won't be able to spend it.

> Implicitly, an output script can be almost as large as the transaction containing it, and a transaction can be almost as large as the block containing it.

An output script with zero length can be spent by an input script containing OP_TRUE. Anyone can create that input script, which means anyone can spend an empty output script.

Bitcoin Core's policy limits output scripts to a few *standard transaction outputs*. This is to support anyone-can-spend upgrades and to encourage best practices.

### Witness Structure

Witnesses(Signatures and public keys) are the values used to solve the math problems that protect bitcoins. They need to be included in the transactions where they're used in order for full nodes to verify them.

Earlier the signatures were included in the input script. But this created several problems when devs tried to implement contract protocols.

#### Circular Dependencies

Many contract protocols involve a series of transactions that are signed out of order. When the signature is included in the input script, this creates a circular dependency where the signature of one transaction depends on the signature of another transaction that depends on the first transaction.(Read [example](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch06_transactions.adoc#circular-dependencies) in the book for better understanding)

![Circular Dependency](../../../assets/images/circular-dependency.png)

#### Third party transaction malleability

It is possible to solve the same script in different ways. This means that any third party can create conflicting transactions with different transaction IDs by manipulating the script. This is *unwanted transaction malleability*.

#### Second-Party Transaction Malleability

Let's say Alice and Bob create a transaction(tx0) that puts their funds in a multisig address and create a refund transaction(tx1). The refund transaction points to the previous transaction. Bob can easily change the signature of the first transaction and create a new transaction(tx2) if this new conflicting transaction(tx2) gets confirmed, the refund transaction will be invalid.

#### Segregated Witnesses

The abstract name for the data held by an input script is a *witness*. The idea of separating the witness data from the transaction data is called *Segregated Witness*(SegWit). It was a *soft fork*.

The soft fork was based on anyone-can-spend output scripts. The script starts with any numnber(0 to 16) followed by 2 to 40 bytes of data called the *witness program*. This is the SegWit template.

For SegWit, old nodes *allowed* an empty input script wherease new nodes *required* an empty input script. It kept the witness from affecting the txid, eliminationg circular dependencies and malleability.

The users of the SegWit output script templates would need a new field. That field is called the *witness structure*.

The initial release of Bitcoin introduced scripts that allow bitcoins to be received to output scripts and spent with input scripts. SegWit allowed bitcoins to be received to witness programs and spent with witness structure.

#### Witness Structure Serialization

![Witness Structure Serialization](https://github.com/bitcoinbook/bitcoinbook/raw/develop/images/mbc3_0605.png)

There is one witness stack for evey input in a transaction(implied by the input count).

The witness structure of a particular input starts with the count of the number of elements(*witness items*) it contains. Each item is prefixed by a compactSize int to indicate size.

### Lock Time

This is the final field. Bitcoin's earliest known soft fork included the following rules for a valid transaction:

- It indicates the eligibility to be included in any block by setting the locktime to zero.
- It sets a locktime value of less than 500 million to indicate a block height after which the transaction can be included.
- It sets a locktime value of 500 million or more to indicate a Unix epoch time after which the transaction can be included.

### Coinbase Transactions

It is the first transaction in each block. It gives the miner the option to claim transaction fees and the block subsidy(upto block 6.72 million). The collective amount is called the *block reward*.

#### Special rules for coinbase transactions

- The input count must be one.
- The outpoint for the input should be a null txid and max index of 0xFFFFFFFF
- The field length of the input script should be between 2 and 100 bytes. That field is called the *coinbase*.
- Sum of the output values must be less than or equal to the sum of the block subsidy and transaction fees.
- A block that contains a SegWit transaction that commits to all of the transactions in the block(witness commitment).

A coinbase transaction cannot be spent until the block it is in has 100 confirmations. This is called the *coinbase maturity* or *maturity rule*.

### Weight and Vbytes

The modern unit of measurement in bitcoin is called weight. An alternative unit is called *vbytes*.

> ### **1 weight = 4 vbytes**

Blocks are limited to 4 million weight. To calculate the weight of a field in a transaction, the size of the serialized field in bytes is multiplied by a factor(4 or 1).

<table id="weight_factors">
<caption>Weight factors for all fields in a Bitcoin transaction</caption>
<thead>
<tr>
<th><p>Field</p></th>
<th><p>Factor</p></th>
<th><p>Weight in Alice’s Tx</p></th>
</tr> </thead>
<tbody>
<tr>
<td><p>Version</p></td>
<td><p>4</p></td>
<td><p>16</p></td>
</tr>
<tr>
<td><p>Marker &amp; Flag</p></td>
<td><p>1</p></td>
<td><p>2</p></td>
</tr>
<tr>
<td><p>Inputs Count</p></td>
<td><p>4</p></td>
<td><p>4</p></td>
</tr>
<tr>
<td><p>Outpoint</p></td>
<td><p>4</p></td>
<td><p>144</p></td>
</tr>
<tr>
<td><p>Input script</p></td>
<td><p>4</p></td>
<td><p>4</p></td>
</tr>
<tr>
<td><p>Sequence</p></td>
<td><p>4</p></td>
<td><p>16</p></td>
</tr>
<tr>
<td><p>Outputs Count</p></td>
<td><p>4</p></td>
<td><p>4</p></td>
</tr>
<tr>
<td><p>Amount</p></td>
<td><p>4</p></td>
<td><p>64 (2 outputs)</p></td>
</tr>
<tr>
<td><p>Output script</p></td>
<td><p>4</p></td>
<td><p>232 (2 outputs with different scripts)</p></td>
</tr>
<tr>
<td><p>Witness Count</p></td>
<td><p>1</p></td>
<td><p>1</p></td>
</tr>
<tr>
<td><p>Witness items</p></td>
<td><p>1</p></td>
<td><p>66</p></td>
</tr>
<tr>
<td><p>Lock time</p></td>
<td><p>4</p></td>
<td><p>16</p></td>
</tr>
<tr>
<td><p><strong>Total</strong></p></td>
<td><p><em>N/A</em></p></td>
<td><p><strong>569</strong></p></td>
</tr>
</tbody>
</table>

```bash
$ bitcoin-cli getrawtransaction 466200308696215bbc949d5141a49a41\
38ecdfdfaa2a8029c1f9bcecd1f96177 2 | jq .weight
569
```

Weights were designed to encourage the use of SegWit and discourage the use of legacy transactions and avoid unecoomic outputs.

We have discussed a new serialization format but legacy serialization does not include the marker, flag, and witness structure fields.
