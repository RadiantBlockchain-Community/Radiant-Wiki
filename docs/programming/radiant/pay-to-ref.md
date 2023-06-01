# Pay to Ref

Pay to ref is a powerful new script built using Radiant's unique induction proof system. This script provides a way of composing contracts from multiple outputs, introducing efficiencies and flexibility previously very difficult on UTXO based blockchains.

```
<REF> OP_REFTYPE_UTXO OP_0 OP_NUMNOTEQUAL
```

The above script uses `OP_REFTYPE_UTXO` to take a given ref from the stack and return the type of any ref found in the transaction's inputs. If the returned value is not equal to zero then we know the ref has been found in the inputs. We can check for any ref type or a specific type.

`OP_REFTYPE_UTXO` returns one of the following:

- 0 - Ref not found in inputs
- 1 - Ref found in inputs, of normal type
- 2 - Ref found in inputs, of singleton type

## Composing contracts from multiple UTXOs

This script can be used to break a contract up into multiple parts. For example a complex NFT contract, split into a token contract and ownership contract:

**UTXO for contract functions**

```
// Contract functions
// function 1...
// function 2...
OP_PUSHINPUTREFSINGLETON <ref1> OP_DROP
<ref2> OP_REFTYPE_UTXO OP_2 OP_NUMEQUAL
```

**UTXO for token ownership/control**

```
OP_PUSHINPUTREFSINGLETON <ref2> OP_DROP
OP_DUP OP_HASH160 <pubKeyHash> OP_EQUALVERIFY OP_CHECKSIG
```

The above contract is composed of two outputs. A singleton ref has been assigned to each output. The first UTXO can only be spent if the second "control" UTXO is also an input.

We can now handle token transfers using only the P2PKH output and not have to touch the rest of the contract. Since the token contract doesn't contain the P2PKH script, only a pay to ref, it is automatically carried forward with the transfer. For wallets to support transfers of this token, they only need to handle a simple singleton + P2PKH script and not be concerned with custom functionality or parsing custom contract templates.

----DIAGRAM----
> Transferring control of a token contract

The two singletons are best created as a contiguous set, so if any script needs to read from another part of the contract, it can check for the existence of a ref within its own set to verify the authenticity of the input. This also makes it easy for wallets to find all parts of the contract.

In this example we have only used two outputs, but this can be extended to compose contracts from three or more outputs.

## Upgrading contracts

Another powerful use case is to extend a contract's functionality by permitting the UTXO to be spent alongside one of a range of refs. These contract extensions can be defined at a later date, providing a way of upgrading contracts or adding new functionality.

The developer must plan this in advance, by keeping some refs in reserve, coded into the contract so they can be deployed and distributed to users later.

For example, a contract that allows locking conditions to be delegated to an input containing any ref from a specific transaction hash. The ref index is provided as a parameter in the unlocking script:

```
<extensionTxId> OP_SWAP OP_CAT OP_REFTYPE_UTXO OP_0 OP_NUMNOTEQUAL
```

The above script takes a ref index from the unlocking script, appends to the transaction ID to construct the ref and checks if the ref exists in an input.

Care must be taken to ensure the user remains in control of their coins and complete control is not given over to the developer. Requiring the owner's signature will ensure the developer cannot spend the coins.
