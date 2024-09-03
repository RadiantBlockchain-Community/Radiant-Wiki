# ElectrumX

Radiant's implementation of ElectrumX has modifications to support Glyphs.

## Querying

ElectrumX indexes each script with the ref zeroed out. The zeroed ref acts as a wildcard meaning a wallet can fetch all transactions for an address by using a ref with 36 zero bytes in the token script.

For example:

```
OP_PUSHINPUTREFSINGLETON 000000000000000000000000000000000000000000000000000000000000000000000000 OP_DROP OP_DUP OP_HASH160 <address> OP_EQUALVERIFY OP_CHECKSIG
```

This script is hashed and can be used with `listunspent` and script hash subscriptions. This prevents having to perform many queries for each token ref or having to know token refs in advance.

## Ref get

A glyph ref will point to an outpoint in the commit transaction, therefore this alone cannot be used to fetch the token payload. To simplify this task, the `ref.get` method will return the first and last locations of a ref.

The first location will be the reveal transaction. The last location is used to track the current location of a contract.

## List unspent

The `listunspent` method is improved to return an array of all refs contained in the script. Since refs are zeroed when querying for tokens, the wallet does not know the token ref in the returned UTXOs and would need to query each UTXO individually to determine the token.

With refs returned as part of the `listunspent` response, the wallet can build scripts without the additional queries.
