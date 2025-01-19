---
title: Mastering Bitcoin - Chapter 4
author: Rishabh
pubDatetime: 2024-07-02T12:30:00Z
slug: mastering-bitcoin-ch-04
featured: false
draft: true
tags:
  - Bitcoin
  - Mastering Bitcoin
description:
    Notes on the fourth chapter of Mastering Bitcoin by Andreas M. Antonopoulos
---

## Keys and Addresses

![Transaction Chain from original Bitcoin Paper](https://github.com/bitcoinbook/bitcoinbook/raw/develop/images/mbc3_aain01.png)

A receiver accepts bitcoins to a public key signed by the spender. In order to spend those bitcoins, the spender must sign the transaction with the corresponding private key.

## Public Key Cryptography

Cryptography enables the creation of unforgeable digital signatures based on algorithms like elliptic curve. The private key(*k*) is a number, picked at random. From the private key, we use elliptic curve multiplication to derive the public key(*K*).

Asymmetric cryptography is used for it's ability to generate *digital signatures*. The signature can be produced by the private key and verified by the public key.

### Private Keys

A private key is a 256 bit number picked at random(toss a coin 256 times and you have the binary digits of a random private key you can use in a Bitcoin wallet). It is used to create signatures used to spend bitcoins.

The private key can be any number between 0 and n-1 where *n* is defined as the order of the elliptic curve.

### Elliptic Curve

It is a discrete logarithmic problem as expressed by addition and multiplication of points on the curve.

![Elliptic Curve](https://wiki.bitcoinsv.io/images/thumb/0/08/Elliptic-Curve-E-0-7-Real-Number.png/300px-Elliptic-Curve-E-0-7-Real-Number.png)

It is derived by the following function:

$$
{y^2 = (x^3 + 7)}~\text{over}~(\mathbb{F}_p)
$$
$$
OR
$$
$$
{y^2 \mod p = (x^3 + 7) \mod p}
$$

The $\mod p$ indicates that the curve is defined over the field of integers modulo a prime number $p$ also written as $\mathbb{F}_p$, where $p = 2^{256} – 2^{32} – 2^{9} – 2^{8} – 2^{7} – 2^{6} – 2^{4} – 1$

Since the curve is defined over a finite field of prime numbers, it looks like a pattern of dots scattered in two dimensions.

In elliptic curve, there is a "point at infinity" that roughly corresponds to zero in real numbers.

#### Addition

There is also a $+$ operator w.r.t elliptic curves.

Given two points $P$ and $Q$ on the curve, the sum of the points is calculated by drawing a line through $P$ and $Q$ and finding the third point where the line intersects the curve. The result of $P + Q$ is the reflection of the intersection point across the x-axis.

Special cases that need "point at infinity":

- $P$ = $Q$: The line is tangent to the curve at $P$ and the result is the point at infinity.
- In some cases($P$ and $Q$ have same x-coordinate but different y-coordinate), the line is vertical and the result is the point at infinity.

The point of infininty is oftne represented as $O$.

$P$ + $O$ = $P$

The elliptic curve addition is associative i.e. $(P + Q) + R = P + (Q + R)$

#### Multiplication

$$
kP = P + P + P + ... + P
$$

### Public Keys

The public key is calculated from: $K = k * G$ where $G$ is the *generator point* of the curve and $k$ is the private key. The generator point is specified as part of the *secp256k1* standard. A private key will always generate the same public key.

```bash
Gx = 0x79BE667EF9DCBBAC55A06295CE870B07029BFCDB2DCE28D959F2815B16F81798
Gy = 0x483ADA7726A3C4655DA4FBFC0E1108A8FD17B448A68554199C47D08FFB10D4B8
```

The reverse operation is called "finding the discrete logarithm" and is computationally infeasible.

![Elliptic Curve Multiplication](https://github.com/bitcoinbook/bitcoinbook/raw/develop/images/mbc3_0404.png)

## Output and Input Scripts

The first version of Bitcoin had payments sent to an output script and spent by an input script.

The earlier version of Bitcoin allowed a spender to enter the receiver's IP address, but that was later removed for being problematic.Every connection to the IP was given a public key(P2PK).

If Alice had to pay bob, she would construct the following output:

```plaintext
<Bob's Public Key> OP_CHECKSIG
```

The input script to spend the output would be:

```plaintext
<Bob's Signature>
```

The script is executed by the Bitcoin Scripting Language. The input script and output script are combined in a stack form(input first).

```plaintext
<Bob's Signature> <Bob's Public Key> OP_CHECKSIG
```

Read: [A primer on the Bitcoin Scripting Language](https://rishkwal.hashnode.dev/understanding-bitcoin-script)

If the signature is correct, `OP_CHECKSIG` will return 1, else 0. The script passes if the stack is empty and the top element is 1. The transaction is valid if all scripts pass.

### P2PKH

One of the downsides of using IP addresses was that the receiver had to be online at their IP address and it needs to be accessible from outside world which is not practical.

Another problem was having to give receivers a long public key. To solve this, the public key was hashed.

The original version of Bitcoin made commitments to public keys by hashing the key with SHA256 first and then with RIPEMD160 for unstated reasons. This produced a 20 byte commitment.

$$
{A = RIPEMD160(SHA256(K))}
$$

Usage:

Output Script:

```plaintext
OP_DUP OP_HASH160 <Bob's Hash> OP_EQUALVERIFY OP_CHECKSIG
```

Input Script:

```plaintext
<Bob's Signature> <Bob's Public Key>
```

Together:

```plaintext
<Bob's Signature> <Bob's Public Key> OP_DUP OP_HASH160 <Bob's Hash> OP_EQUALVERIFY OP_CHECKSIG
```

Explanation: First Bob's signature goes on the stack followed by Bob's public key. The public key is duplicated and hashed. The hash is compared with the hash in the output script. If the hashes match, the signature is verified. P2PKH allows Alice's payment to contain only 20 byte commitment instead of 65 byte public key.

### Base58check Encoding

Decimal system uses 10 numerals, hexadecimal uses 16. Similarly base64 uses 64 characters(26 uppercase, 26 lowercase, 10 digits, and 2 symbols). Base58check uses 58 characters(uppercase, lowercase, and digits) excluding 0, O, I, and l to avoid confusion and no symbols. Bitcoin addresses are encoded in base58check.

To convert into base58check we first prefix the date with "version byte". For example the prefix 0x00 indicates the data should be used as the commitment in a legacy P2PKH output script.

To add extra security against typos, a checksum is added to the end of the commitment. The checksum is calculated by hashing the commitment twice with SHA256 and taking the first 4 bytes of the result. The checksum is appended to the address and encoded in base58.

```plaintext
checksum = SHA256(SHA256(prefix + data))[:4]
```

Result: `base58encode(prefix + data + checksum)`

![Base58check Encoding](https://www.oreilly.com/api/v2/epubs/9781491902639/files/images/msbt_0406.png)

<table id="base58check_versions">
<caption>Base58check version prefix and encoded result examples</caption>
<thead>
<tr>
<th>Type</th>
<th>Version prefix (hex)</th>
<th>Base58 result prefix</th>
</tr>
</thead>
<tbody>
<tr>
<td><p>Address for pay to public key hash (P2PKH)</p></td>
<td><p>0x00</p></td>
<td><p>1</p></td>
</tr>
<tr>
<td><p>Address for pay to script hash (P2SH)</p></td>
<td><p>0x05</p></td>
<td><p>3</p></td>
</tr>
<tr>
<td><p>Testnet Address for P2PKH</p></td>
<td><p>0x6F</p></td>
<td><p>m or n</p></td>
</tr>
<tr>
<td><p>Testnet Address for P2SH</p></td>
<td><p>0xC4</p></td>
<td><p>2</p></td>
</tr>
<tr>
<td><p>Private Key WIF</p></td>
<td><p>0x80</p></td>
<td><p>5, K, or L</p></td>
</tr>
<tr>
<td><p>BIP32 Extended Public Key</p></td>
<td><p>0x0488B21E</p></td>
<td><p>xpub</p></td>
</tr>
</tbody>
</table>

![Creation of a Bitcoin address](https://www.oreilly.com/api/v2/epubs/9781491902639/files/images/msbt_0405.png)

## Compressed Public Keys

The public key is 65 bytes long. The first byte is 0x04 and the next 32 bytes are the x-coordinate and the last 32 bytes are the y-coordinate. The y-coordinate can be calculated from the x-coordinate. If we know the x-coordinate we can calculate the y-coordinate by solving $y^2 mod p = (x^3 + 7) mod p$.
There are two possible values for y, one even and one odd. The compressed public key is 33 bytes long. The first byte is 0x02 if y is even(in binary) and 0x03 if y is odd. The next 32 bytes are the x-coordinate.

Most Bitcoin wallets use compressed public keys. However wallets still need to support legacy uncompressed public keys so that they can verify older transactions. Failure to scan for both types of public keys can result in loss of funds. To solve this, when private keys are exported, they are exported in a WIF(Wallet Import Format) that indicates that the private key has been used to generate a compressed public key.

## Legacy Pay to Script Hash(P2SH)

The reciever can want to put some constraints on how the bitcoins can be spent. Orginially the sontraint was simply that input needs to provide an appropriate signature.

The BIP16 upgrade allowed an output script to commit to a redemption script. In order to spend the bitcions, the spender needs to provide a redeem script that matches this commitment.

### Example

Bob wants to require two signatures to spend his Bitcoins.

He creates a redeem script that requires two signatures:

```plaintext
<pubkey 1> OP_CHECKSIGVERIFY <pubkey 2> OP_CHECKSIG
```

Bob hashes the redeem script using HASH160 i.e. SHA256(RIPEMD160(redeem script)) and gets the hash:

```plaintext
OP_HASH160 <commitment> OP_EQUAL
```

To spend his bitcoins, bob uses the following input script:

```plaintext
<sig1> <sig2> <redeem script>
```

Full nodes will verify the redeem script by executing it and checking if the hashes match.

The script will be accepted if everything matches and the transaction will pass.

P2SH addresses are also created with base58check. The version prefix is set to 5 which results in an encoded address starting with 3.

P2PKH and P2SH are only ones using base58check and are now replaced by bech32 addresses. There were also probable collision attacks for these addresses.

## Bech32 Addresses

One of the advantages of P2SH was that spender didn't need to know the details of the script the receiver used. 