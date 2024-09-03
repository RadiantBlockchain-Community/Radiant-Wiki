# Relationships

The Glyph protocol provides `in` and `by` arrays in the token payload for defining relationships to other tokens, such as NFT collections. Relationships can also be defined in custom properties.

For a token to be related its ref must be present in the commit transaction. Wallets must verify this by checking each related token's ref is in an output of the commit. This prevents authorized users creating relationships, such as assigning an author or adding NFTs to a collection they don't control.

## Delegates

Having a related token in a commit is not always practical if there are many thousands of mint transactions with the same relationship. For example, minting a large NFT collection with the same container and author tokens. To solve this a token delegate may be used.

The delegate is a temporary normal ref that links to one or more token refs such as a user or container token.

A delegate is created by a transaction containing the related tokens, and one or more outputs with the following script:

```
OP_PUSHINPUTREF <ref> OP_DROP
OP_DUP OP_HASH160 <address> OP_EQUALVERIFY OP_CHECKSIG // P2PKH
```

Any number of delegates may be created to simplify bulk mints.

## Verifying related tokens

When a wallet does not find a related token's ref in the commit transaction, it must look for delegates within the commit script. Delegate refs must be required using a `OP_REQUIREINPUTREF` opcode in the commit script.

If the wallet finds required refs, it fetches the delegate creation transaction and looks for related tokens in its outputs. If the refs are found in the delegate creation transaction then the relationship is valid.

## Custom relationships

Developers are free to define their own relationships separate to the `in` and `by` arrays.
