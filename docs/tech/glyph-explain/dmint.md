# Decentralized mints

Decentralized mints (dmint) are mineable tokens that can be claimed by anyone using proof-of-work. These tokens are created with protocol `[1, 4]` and are locked in a layer one smart contract.

### PoW algorithm

Mineable Glyphs use the follow proof-of-work algorithm:

```
hash = sha256(sha256(
    sha256(currentLocationTxid + contractRef) +
    sha256(anyInputHash + anyOutputHash) +
    nonce
))
```

Resulting hash must be below the target.

`anyInputHash` and `anyOutputHash` must be the hash of any input or output in the transaction. This allows work to be bound to the miner's address and prevents nonces being stolen. This will typically be a pay-to-public-key-hash but any script can be used. The contract will verify these scripts exist.

### Further information

For more information including code for the mining smart contract see the Glyph Miner GitHub.
