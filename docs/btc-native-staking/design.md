# Design

## BTC Native Staking Transaction Design

## Background

Core DAO’s methodology for integrating bitcoin staking centers on [CLTV timelock](https://en.bitcoin.it/wiki/Timelock#CheckLockTimeVerify). The `OP_CHECKLOCKTIMEVERIFY` (CLTV) timelock is a specific opcode used in Bitcoin's scripting language that allows for creating conditions based on time or block height before bitcoins can be spent from a transaction output. This provides a way to create outputs that are time-locked, meaning they cannot be spent until a certain condition related to time or block height is met.

<figure><img src="../.gitbook/assets/spaces_DLZv2z2IzD1yPifdhZKK_uploads_ucqE9D7oqg082epSzYRK_staking-tx-design (5).webp" alt=""><figcaption></figcaption></figure>

## Transaction Structure

### Staking transaction

A BTC staking transaction should have two/three outputs, which are

* `P2SH/P2WSH` type output, with time-lock enabled redeem script
* `OP_RETURN` type output, with Core chain staking information
* (_Optional_) Change address

> **Note**&#x20;
>
> There are **no** restrictions on inputs.

<figure><img src="../.gitbook/assets/staking-tx-design (1).png" alt=""><figcaption></figcaption></figure>

### Withdrawal transaction

When the time-lock ends, the locked UTXO can be spent using the redeem script.

<figure><img src="../.gitbook/assets/staking-tx-design (2).png" alt=""><figcaption></figcaption></figure>

## Script Design

### P2SH/P2WSH Output

* Core supports both `P2SH` and `P2WSH` outputs for BTC staking.
* The construction of `P2SH` type output is as follows
  * `OP_HASH160 <RIPEMD160(SHA256(RedeemScript))> OP_EQUAL`
* The construction of `P2WSH` type output is as follows
  * `OP_0 <SHA256(RedeemScript)>`

#### Redeem Script

The `RedeemScript` should start with a CLTV time lock. Here are a few common types.

* When using a public key `<CLTV timelock> OP_CLTV OP_DROP <pubKey> OP_CHECKSIG` and the corresponding unlocking script in the withdrawal transaction is `<sig> <RedeemScript>`
* When using a public key hash (most recommended) `<CLTV timelock> OP_CLTV OP_DROP OP_DUP OP_HASH160 <pubKey Hash> OP_EQUALVERIFY OP_CHECKSIG` and the corresponding unlocking script in the withdrawal transaction is `<sig> <pubKey> <RedeepScript>`
* When using multi signature address `<CLTV timelock> OP_CLTV OP_DROP M <pubKey1> <pubKey1> ... <pubKeyN> N OP_CHECKMULTISIG` and the corresponding unlocking script in the withdrawal transaction is `OP_0 <sig1> ... <sigM> <RedeemScript>` The amount and duration of BTC locked in this output will be used for the calculation of validator election and reward distribution on the Core chain.

> **Note** There are _minimal requirements_ on both **amount** and **duration** to make the staking eligible on Core. A user should at least stake **0.01 BTC** (less transaction fees) for at least **7 days** (`CLTV timestamp - transaction confirmation timestamp > 7 days`).

### OP\_RETURN Output

The `OP_RETURN` output should contain all staking information in order, and be composed in the following format:

* **`OP_RETURN`:** identifier `0x6a`
* **`LENGTH`:** which represents the total byte length after the `OP_RETURN` opcode. _Note that all data has to be pushed with its appropriate size byte(s)_.
* **`Satoshi Plus Identifier`:** (**SAT+**) 4 bytes
* **`Version`:** (**0x01**) 1 byte
* **`Chain ID`:** (**1115** for Core Testnet and **1116** for Core Mainnet) 2 bytes
* **`Delegator`:** The Core address to receive rewards, 20 bytes
* **`Validator`:** The Core validator address to stake to, 20 bytes
* **`Fee`:** Fee for relayer, 1 byte, range \[0,255], measured in CORE
* (_Optional_) **`RedeemScript`**
* (_Optional_) **`Timelock`:** 4 bytes

**Key Points to Ensure**

* Any bytes that can translate to a number should use `OP_number` (`{0}` should use `OP_0` instead of `0x0100`, `{16}` should use `OP_16` instead of `0x0110`)
* Any bytes with lengths smaller than `0x4c (76)` is pushed with 1 byte equal to the size `(byte[10] -> 10 + byte[10]; byte[70] -> 70 + byte[70])`
* Any bytes bigger than or equal to `0x4c` is pushed by using `0x4c` (ie. `OP_PUSHDATA`) followed by the length followed by the data `(byte[80] -> OP_PUSHDATA + 80 + byte[80])`
* Any bytes with length bigger than `255` uses `0x4d` (`OP_PUSHDATA2`)
* Any bytes with length bigger than `65535` (`0xffff`) uses `0x4e` (`OP_PUSHDATA4`)

Either `RedeemScript` or `Timelock` must be available, the purpose is to allow relayer to obtain the `RedeemScript` and submit transactions on the Core chain. If a `RedeemScript` is provided, relayer will use it directly. Otherwise, relayer will construct the redeem script based on the timelock and the information in the transaction inputs. You can find more information about the relayer role in the below section.

## Transaction Examples

### Staking transaction

[https://mempool.space/tx/9f5c66d5f90badafd537df44326f270aa64b7cc877ef68c3b69ed436870a3512](https://mempool.space/tx/9f5c66d5f90badafd537df44326f270aa64b7cc877ef68c3b69ed436870a3512)

<figure><img src="../.gitbook/assets/staking-tx-design (3).png" alt=""><figcaption></figcaption></figure>

#### P2WSH output

This is the staking output and it is a standard P2WSH address. The redeem script used for this output is `041f5e0e66b17576a914c4b8ae927ff2b9ce218e20bf06d425d6b68424fd88ac`

```jsx
OP_PUSHBYTES_4 1f5e0e66
OP_CLTV
OP_DROP
OP_DUP
OP_HASH160
OP_PUSHBYTES_20 c4b8ae927ff2b9ce218e20bf06d425d6b68424fd
OP_EQUALVERIFY
OP_CHECKSIG
```

The script looks very similar to a normal `P2PKH` redeem script except it starts with a timelock `OP_PUSHBYTES_4 1f5e0e66 OP_CLTV OP_DROP`.

The redeem script hash used in this P2WSH output is `SHA256(041f5e0e66b17576a914c4b8ae927ff2b9ce218e20bf06d425d6b68424fd88ac)` which results to `3dd731ae1c3ce32cfbec4ea82c855e027adf5fddca6d0118029b0ba15e44e0e9` .

Here is an online tool to generate `P2WSH` `sha256` hash value from redeem script, by which you can verify the above calculation: [https://www.btcschools.net/bitcoin/bitcoin\_tool\_sha256.php](https://www.btcschools.net/bitcoin/bitcoin\_tool\_sha256.php)

#### OP\_RETURN output

The full hex of this output is `6a4c505341542b01045bde60b7d0e6b758ca5dd8c61d377a2c5f1af51ec1a9e209f5ea0036c8c2f41078a3cebee57d8a47d501041f5e0e66b17576a914c4b8ae927ff2b9ce218e20bf06d425d6b68424fd88ac` , where

* `6a` is op\_return opcode
* `4c50` is the total byte length after the `OP_RETURN` opcode \[1]
* `5341542b` SAT+, which is satoshi plus identifier
* `01` is version
* `045b` 1115, which is chain id  (**1115** for Core Testnet and **1116** for Core Mainnet)&#x20;
* `de60b7d0e6b758ca5dd8c61d377a2c5f1af51ec1` is the reward address
* `a9e209f5ea0036c8c2f41078a3cebee57d8a47d5` is the validator address
* `01` is relayer fee, measured in CORE
* `041f5e0e66b17576a914c4b8ae927ff2b9ce218e20bf06d425d6b68424fd88ac` is redeem script, which is explained in the above section.

\[1] Any bytes bigger than or equal to `0x4c` is pushed by using `0x4c` (ie. `OP_PUSHDATA`) followed by the length followed by the data (`byte[80] -> OP_PUSHDATA + 80 + byte[80])`

### Withdrawal Transaction

[https://mempool.space/tx/dc02ddc54ff82ba561f4d82429338d1df50377fcce0725bc764b9b2562d10832](https://mempool.space/tx/10182ad08fdb0469ab3d91d1bb340c7b0cbd858ad8865f6b6ddf76e3806ba889)

This transaction spent the P2WSH time-lock output from the above staking transaction

<figure><img src="../.gitbook/assets/staking-tx-design (4).png" alt=""><figcaption></figcaption></figure>

In the input, the redeem script `041f5e0e66b17576a914c4b8ae927ff2b9ce218e20bf06d425d6b68424fd88ac` is provided to spend it. Since the time lock `1f5e0e66` (660e5e1f after reverting bytes, which is 1712217631 unix timestamp) has already expired, the UTXO was spent successfully.

> **Note** > Code samples of constructing the staking and withdrawal transactions on Bitcoin network will be provided soon.

## Role of Relayers

In a strict sense, the BTC Native Staking process consists of two steps

* Stake on the Bitcoin network
* Submit the confirmed BTC staking transaction to the Core chain

To make the entire process more convenient, Core DAO introduces the role of relayers. Relayers can help users submit transactions to the Core network after the staking transaction is confirmed on the Bitcoin network. Since it is necessary to verify the transaction on the Core network with the embedded Bitcoin light client, relayers needs to obtain the corresponding `RedeemScript` of the `P2SH/P2WSH` output. To meet this requirement, we suggest users to either

* Put the entire `RedeemScript` at the end of the `OP_RETURN` output, if the script is short. e.g. a `RedeemScript` constructed using public key hash as shown in the sample above.
* Set the receiving address of the staking transaction as their own so relayers can extract useful information from the transaction input and compose the `RedeemScript` by themselves. E.g.
  * If it's a normal address, the `pubkey` or `pubkey hash` should be set as the input's corresponding public key when constructing the `RedeemScript`.
  * If it is a multi-signature address, the corresponding multi-signature address's public key should be set when constructing the `RedeemScript`.
