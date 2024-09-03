# Non-fungible tokens

Non-fungible tokens must be created with the protocol `p` field set to `2`.

The script is simply a singleton ref followed by a P2PKH or other locking script:

```
OP_PUSHINPUTREFSINGLETON <ref> OP_DROP
OP_DUP OP_HASH160 <address> OP_EQUALVERIFY OP_CHECKSIG // P2PKH
```

Radiant rules enforce that there can only ever be one instance of a singleton ref.

## Transfer

NFTs are transferred in a similar way to regular RXD photons. The singleton ref is carried forward with each spend and may be at any position in the transaction inputs or outputs.

Similar to fungible tokens, any number of different tokens may be present in a transaction.

## Melt

Melting a non-fungible token can be done in two ways:

- Omit the ref from the outputs
- Spend the NFT to an unspendable op return script containing the ref
