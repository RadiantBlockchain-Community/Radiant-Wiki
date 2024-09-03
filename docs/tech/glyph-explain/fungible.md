# Fungible tokens

Fungible tokens must be created with the protocol `p` field set to `1`. This informs wallets that the token uses a standard fungible token script.

The script is a P2PKH followed by a script to enforce fungible token rules:

```
// P2PKH
OP_DUP OP_HASH160 <address> OP_EQUALVERIFY OP_CHECKSIG

OP_STATESEPARATOR

// Fungible token ref
OP_PUSHINPUTREF <ref>

// Get count of ref used in outputs
OP_REFOUTPUTCOUNT_OUTPUTS OP_INPUTINDEX

// Get a hash of the script after state separator
OP_CODESCRIPTBYTECODE_UTXO OP_HASH256 OP_DUP

// Sum token value for all outputs
OP_CODESCRIPTHASHVALUESUM_UTXOS

// Sum token value for all inputs
OP_OVER OP_CODESCRIPTHASHVALUESUM_OUTPUTS

// Input sum must be greater than or equal to output sum
OP_GREATERTHANOREQUAL OP_VERIFY

// To prevent supply inflation, ref must be used within this script and ref count must be equal to contract count
OP_CODESCRIPTHASHOUTPUTCOUNT_OUTPUTS OP_NUMEQUALVERIFY
```

The script after the P2PKH must be unchanged for every transfer.

The protocol permits locking scripts other than P2PKH, such as multisig, time locks and other more complex scripts. The only requirement is that the script after the state separator is unchanged.

## Transfer

Tokens are transferred in a similar way to regular RXD photons. The contract and ref are carried forward with each spend and UTXOs can be split and merged. Tokens may be at any position in the inputs or outputs. A transaction may also contain any number of different tokens.

## Melt

The contract permits output value sum to be less than input value sum. Melting fungible tokens only requires making the output value less than input value. The RXD photons backing the tokens are then reclaimed.
