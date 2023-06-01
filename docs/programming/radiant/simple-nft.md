# Non-Fungible Tokens on Radiant

Radiant's singleton references are ideal for the creation of NFTs. A singleton ref is a type of reference which can only ever exist in a single UTXO. A transaction is invalid if a singleton ref is pushed to more than one output. References cannot change type, so a singleton ref must be created as a singleton. Therefore we can be sure that there has only ever been one instance of a singleton ref.

The opcode for pushing a singleton ref is `OP_PUSHINPUTREFSINGLETON`.

## A simple NFT contract

We will define a contract for an immutable NFT by adding a singleton ref to a standard P2PKH script:

```
OP_PUSHINPUTREFSINGLETON <ref> OP_DROP
OP_DUP OP_HASH160 <pubKeyHash> OP_EQUALVERIFY OP_CHECKSIG
```

Following Radiant's ref rules, the ref must match an outpoint or a ref of the same type in an input. If the ref matches an outpoint then this is the "mint" transaction. `OP_PUSHINPUTREFSINGLETON` leaves the ref on the stack, and since we don't need it later in the script it is immediately dropped.

-------DIAGRAM-------
img/simple-nft.png
![DIAGRAM SIMPLE NFT](/img/simple-nft.png)

> Mint and transfer of an NFT, followed by an invalid transaction

As seen in the diagram above, an outpoint in the first transaction is used to create a singleton ref. The referenced output also contains an "immutable token payload" which can be stored as a push data. This is a simple way of associating data with our token, where the token data can be fetched from the referenced output. The payload could be an image or more complex data structure. Token UTXOs are kept light weight by not carrying the payload.

This token protocol can be used for a wide variety of use cases such as tickets, coupons and collectibles. Wallet integration is simple since the script only requires a singleton ref in addition to a standard P2PKH script.


## Melting a token

Melting a token only requires that the token be spent and the singleton ref not pushed forward. Once the singleton does not exist in a UTXO it cannot be recreated.

## Conclusion

Singleton refs are a simple yet effective way of creating an NFT protocol. We have demonstrated how a token protocol can be created with only a small change to a standard P2PKH script. In future articles we will explore how to extend this contract to support mutable data.
