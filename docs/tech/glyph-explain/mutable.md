# Mutable tokens

Token payloads can be made mutable by using a mutability contract and adding protocol `5` to the `p` array in the token payload.

The mutability contract must be created in the mint transaction and assigned a singleton ref with an output index one higher than the token ref (token ref + 1).

The contract uses a "pay to token" script which does not contain a P2PKH or any signature checking opcodes. The contract requires the token to be present in the transaction in order to modify the payload.

Modify `mod` and seal `sl` operation are implemented. Seal is called to make the token immutable and enforces burning of the mutability contract.

## Pay to token

With a "pay to token" script, the mutability contract is always controlled by the token owner. This means every time a token is transferred to a new owner, they automatically get access to updating the token's data. The mutability output never needs to be transferred, only called to update the data.

## Fetching mutable data

If a wallet receives a mutable token, the most recent data can be obtained by looking up the most recent location of the mutability ref and fetching the transaction. Data is encoded similar to a token mint, with the Glyph token payload in the unlocking script.

## Script

```
// SHA256 of payload must be provided
<payloadHash> OP_DROP

OP_STATESEPARATOR

// Mutable contract ref
OP_PUSHINPUTREFSINGLETON <mutableRef>

// Build token ref (mutable ref -1)
OP_DUP 20 OP_SPLIT OP_BIN2NUM OP_1SUB OP_4 OP_NUM2BIN OP_CAT

// Check token ref exists in token output at given refdatasummary index
OP_2 OP_PICK OP_REFDATASUMMARY_OUTPUT OP_4 OP_ROLL 24 OP_MUL OP_SPLIT OP_NIP 24 OP_SPLIT OP_DROP OP_EQUALVERIFY

// Compare ref + scriptsig hash in token output to this script's ref + scriptsig hash
OP_SWAP OP_STATESCRIPTBYTECODE_OUTPUT OP_ROT OP_SPLIT OP_NIP 45 OP_SPLIT OP_DROP OP_OVER 20 OP_CAT OP_INPUTINDEX OP_INPUTBYTECODE OP_SHA256 OP_CAT OP_EQUALVERIFY

// Modify operation
OP_2 OP_PICK 6d6f64 OP_EQUAL OP_IF

// Contract script must exist unchanged in output
OP_OVER OP_CODESCRIPTBYTECODE_OUTPUT OP_INPUTINDEX OP_CODESCRIPTBYTECODE_UTXO OP_EQUALVERIFY

// State script must contain payload hash
OP_OVER OP_STATESCRIPTBYTECODE_OUTPUT 20 OP_5 OP_PICK OP_HASH256 OP_CAT 75 OP_CAT OP_EQUALVERIFY OP_ELSE

// Seal operation
OP_2 OP_PICK 736c OP_EQUALVERIFY OP_OVER OP_OUTPUTBYTECODE d8 OP_2 OP_PICK OP_CAT 6a OP_CAT OP_EQUAL OP_OVER OP_REFTYPE_OUTPUT OP_0 OP_NUMEQUAL OP_BOOLOR OP_VERIFY OP_ENDIF

// Glyph header
OP_4 OP_ROLL ${glyphMagicBytesHex} OP_EQUALVERIFY OP_2DROP OP_2DROP OP_1
```

## Unlocking script

In order to use a "pay to token" contract securely, the data must be signed. Without a signature check opcode in the contract, this must be done in a different way.

The contract requires the token owner to provide the token as an input. In the token's output it must include a hash of the mutability contract's unlocking script. This way, the token owner signs the updated data in the token output, the contract verifies it, and the transaction is not vulnerable to malleation. In addition to the unlocking script hash, the output must have an `OP_REQUIREINPUTREF` requiring the mutability contract as an input. This ref + hash pair is checked by the mutability contract.

To spend the contract the following parameters must be provided in the unlocking script

```
OP_PUSH gly
OP_PUSH mod // Modify operation
OP_PUSH <cbor payload> // Updated token payload
OP_PUSH <contract output index> // Location of mutability contract UTXO in outputs
OP_PUSH <ref+hash index in token output> // Index of the ref+hash in the token output, allowing multiple authorizations in a single transaction (zero for a single update)
OP_PUSH <ref index in token output data summary> // Position of token ref in OP_REFDATASUMMARY_OUTPUT (the sorted list of refs used in all outputs)
OP_PUSH <token output index> // Location of token UTXO in outputs
```

### Authorizing an update

To authorize this update, the token output must have the hash of the above unlocking script following an `OP_REQUIREINPUTREF`. For example:

```
// Require the mutability contract (token ref + 1)
OP_REQUIREINPUTREF a32f3628a967ce64cca282262e63879136f9c3f4f8e1125345a85c71d90b10b402000000
// Hash of mutability contract's unlocking script
OP_PUSH a204a95c54bf73975256898be5f47d7844146845f9b749408b535d12e3ad85a9
// Drop ref and hash
OP_2DROP
// Following is a standard NFT contract
OP_PUSHINPUTREFSINGLETON a32f3628a967ce64cca282262e63879136f9c3f4f8e1125345a85c71d90b10b401000000 OP_DROP
OP_DUP OP_HASH160 3a23c6b9055b533db3900e4755dd58d1234c8582 OP_EQUALVERIFY OP_CHECKSIG
```

The mutability contract will verify that the hash of the provided unlocking script, along with the necessary `OP_REQUIREINPUTREF` are contained in the token UTXO.

### Restoring token script

An optional final step is for the token owner to restore the token script to a standard NFT contract that can be recognized by wallets. The `OP_REQUIREINPUTREF` and hash are no longer required.
