# Overview

Glyphs are implemented using Radiant's UTXO model and operate in a similar way to native Radiant photons. Radiant photons are "colored" with a Radiant ref which acts like a contract identifier for the token. A token is minted by creating a UTXO containing an amount of photons, a standard token contract and a Radiant ref. The contract along with the ref is carried forward with each spend. The contract and ref remain the same for the lifetime of the token, until it is melted.

With refs enforced by miners we can be certain a token is authentic simply by verifying block headers in the same way as native photons.

## Minting

Glyphs are minted using a commit/reveal procedure with token metadata contained in the reveal transaction's unlocking script. The token's ref is created from an input to the reveal transaction. Therefore a glyph's ref will always point to a commit outpoint.

The token payload in the reveal transaction contains data such as name, image, ticker, relationships to other tokens etc.

The protocol does not dictate how the mint transaction should be created. Developers may use any locking script for the commit/reveal procedure, as long as the token payload is correctly encoded in the reveal.

For each ref that is required an input must be prepared. Radiant singleton refs are used for NFTs, so each NFT requires a unique ref.

## Verifying a token

When a wallet receives a token, it extracts the ref from the script and looks up the reveal transaction on the blockchain to obtain the token data.

For example, an NFT script:

```
OP_PUSHINPUTREFSINGLETON a32f3628a967ce64cca282262e63879136f9c3f4f8e1125345a85c71d90b10b401000000 OP_DROP
OP_DUP OP_HASH160 3a23c6b9055b533db3900e4755dd58d1234c8582 OP_EQUALVERIFY OP_CHECKSIG
```

The ref `a32f3628a967ce64cca282262e63879136f9c3f4f8e1125345a85c71d90b10b401000000` refers to an outpoint in a commit transaction.

The reveal will be the transaction that spent this output, with a token payload contained in the unlocking script.

Radiant's implementation of ElectrumX has been improved to simplify the process of finding the reveal transaction.

## Transferring a token

Transferring is done in much the same way as native Radiant photons. For NFTs, the UTXO is spent and the script is moved to a new output. Fungible tokens are similar, except since they use normal refs instead of singletons, token UTXOs may be split and merged.

Any number of different tokens may be provided as inputs and outputs of a transaction. Token transfer rules are all implemented in script, with token input and output values summed within script, ensuring token supply is enforced. Token UTXOs may be positioned anywhere in a transaction's inputs and outputs and there is no restriction on the number of token UTXOs. The Radiant opcodes used to sum token values operate over any number of inputs and outputs.
