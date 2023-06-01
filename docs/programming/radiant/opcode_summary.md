# Radiant Opcodes

The Radiant blockchain introduced a set of opcodes that significantly improves on the capability of the Bitcoin technology it's based on. All these opcodes will be outlined in this article.

## Terms
* **Ref:** A 36 byte reference, either of normal or singleton type. A reference is created from an input's outpoint and can be propagated with every spend of a UTXO. For a ref to exist in an output there must be a matching input ref of the same type or an input with a matching outpoint. The ref type cannot be changed once it is created.
* **Normal ref**: A type of reference that can be propagated to one or more outputs.
* **Singleton ref:** A type of reference that can only ever exist in a single UTXO. Cannot be propagated to multiple outputs.
* **State script:** The part of the script before and not including an `OP_STATESEPARATOR`. Useful for storing state data that can be changed by the contract.
* **Code script:** The part of the script from and including an `OP_STATESEPARATOR`. If no state separator is present, the code script is the entire script.
* **`codeScriptHash`:** The double SHA-256 of the code script.

## Hash functions

SHA-512/256 is the hash function used by Radiant's proof of work algorithm. It is available in script as single and double hash functions. These can be used to validate Radiant block headers in script.

| Word | Hex | Input | Output | Description |
|------|-----|-------|--------|-------------|
| OP_SHA512_256 | ce | bytes | hash | SHA-512/256 |
| OP_HASH512_256 | cf | bytes | hash | Double SHA-512/256 |

## Induction opcodes

| Word | Hex | Input | Output | Description |
|------|-----|-------|--------|-------------|
| OP_PUSHINPUTREF | d0 | ref | ref | Propagates a ref to an output. A ref of normal type must exist in an input or an input must have an outpoint matching the ref. Ref will be left on the stack. |
| OP_PUSHINPUTREFSINGLETON | d8 | ref | ref | Propagates a singleton ref to an output. A ref of singleton type must exist in an input or an input must have an outpoint matching the ref. Ref will be left on the stack. |
| OP_REQUIREINPUTREF | d1 | ref | ref | A ref of normal type must exist in an input. Ref is not propagated.  Ref will be left on the stack. |
| OP_DISALLOWPUSHINPUTREF | d2 | ref | ref | Requires a ref to not exist in any input. Leaves the ref on the stack. |
| OP_DISALLOWPUSHINPUTREFSIBLING | d3 | ref | ref | Requires a ref to not exist in any input sibling. Leaves the ref on the stack. |
| OP_REFTYPE_UTXO | d9 | ref | type | Get the type of a ref from transaction inputs. Returns 0 (not found), 1 (normal type) or 2 (singleton type). |
| OP_REFTYPE_OUTPUT | da | ref | type | Get the type of a ref from transaction outputs. Returns 0 (not found), 1 (normal type) or 2 (singleton type). |
| OP_REFHASHDATASUMMARY_UTXO | d4 | inputIndex | summary | For the input of the given index returns a hash256 of `<nValue><hash256(scriptPubKey)><numRefs><hash(sortedMap(pushRefs))>` |
| OP_REFHASHDATASUMMARY_OUTPUT | d6 | outputIndex | summary | For the output of the given index returns a hash256 of `<nValue><hash256(scriptPubKey)><numRefs><hash(sortedMap(pushRefs))>` |
| OP_REFDATASUMMARY_UTXO | e1 | inputIndex | summary | Return refs used within an input. Pushes a vector of the form `<ref1><ref2><ref3>...`. If an input UTXO does not contain at least one reference, then the 36 byte zeroes are pushed. |
| OP_REFDATASUMMARY_OUTPUT | e2 | outputIndex | summary | Return refs used within an output. Pushes a vector of the form `<ref1><ref2><ref3>...`. If an input UTXO does not contain at least one reference, then the 36 byte zeroes are pushed. |

## State/code script opcodes

| Word | Hex | Input | Output | Description |
|------|-----|-------|--------|-------------|
| OP_STATESEPARATOR | bd | | | Separates the state script from the code script. Only one can exist in a script and it must not exist within an OP_IF. No OP_RETURN can exist in the script when used. |
| OP_STATESEPARATORINDEX_UTXO | be | inputIndex | separatorIndex | Returns the byte index of the state separator for an input. Returns zero if no separator exists. |
| OP_STATESEPARATORINDEX_OUTPUT | bf | outputIndex | separatorIndex | Returns the byte index of the state separator for an output. Returns zero if no separator exists. |
| OP_CODESCRIPTBYTECODE_UTXO | e9 | inputIndex | bytecode | Returns the code script bytecode for an input. |
| OP_CODESCRIPTBYTECODE_OUTPUT | ea | outputIndex | bytecode | Returns the code script bytecode for an output. |
| OP_STATESCRIPTBYTECODE_UTXO | eb | inputIndex | bytecode | Returns the state script bytecode for an output. Does not include the OP_STATESEPARATOR. |
| OP_STATESCRIPTBYTECODE_OUTPUT | ec | outputIndex | bytecode | Returns the state script bytecode for an output. Does not include the OP_STATESEPARATOR. |

## Count and sum opcodes

These opcodes provide a set queries on inputs and outputs by ref, ref hash and code script hash. Counts and sums can be performed over many outputs with a single opcode, meaning an unrolled loop in the script is not required.

| Word | Hex | Input | Output | Description |
|------|-----|-------|--------|-------------|
| OP_REFHASHVALUESUM_UTXOS | d5 | refHash | photons | Returns the total sum of photons for inputs that match a 32 byte hash of the sorted set of ref asset ids. |
| OP_REFHASHVALUESUM_OUTPUTS | d7 | refHash | photons | Returns the total sum of photons for outputs that match a 32 byte hash of the sorted set of ref asset ids. |
| OP_REFVALUESUM_UTXOS | db | ref | photons | Returns the total sum of photons for inputs that contain a ref. |
| OP_REFVALUESUM_OUTPUTS | dc  | ref | photons | Returns the total sum of photons for outputs that contain a ref. |
| OP_REFOUTPUTCOUNT_UTXOS | dd | ref | count | Returns the count of inputs that contain a ref. |
| OP_REFOUTPUTCOUNT_OUTPUTS | de | ref | count | Returns the count of outputs that contain a ref. |
| OP_REFOUTPUTCOUNTZEROVALUED_UTXOS | df | ref | count | Returns the count of inputs that contain a ref and contain zero photons. |
| OP_REFOUTPUTCOUNTZEROVALUED_OUTPUTS | e0 | ref | count | Returns the count of inputs that contain a ref and contain zero photons. |
| OP_CODESCRIPTHASHVALUESUM_UTXOS | e3 | codeScriptHash | photons | Returns the total sum of photons for inputs that have a     `codeScriptHash`. |
| OP_CODESCRIPTHASHVALUESUM_OUTPUTS | e4 | codeScriptHash | photons | Returns the total sum of photons for outputs that have a `codeScriptHash`. |
| OP_CODESCRIPTHASHOUTPUTCOUNT_UTXOS | e5 | codeScriptHash | count | Returns the count of inputs that have a `codeScriptHash`. |
| OP_CODESCRIPTHASHOUTPUTCOUNT_OUTPUTS | e6 | codeScriptHash | count | Returns the count of outputs that have a `codeScriptHash`. |
| OP_CODESCRIPTHASHZEROVALUEDOUTPUTCOUNT_UTXOS | e7 | codeScriptHash | count | Returns the count of inputs that have a `codeScriptHash` and contain zero photons. |
| OP_CODESCRIPTHASHZEROVALUEDOUTPUTCOUNT_OUTPUTS | e8 | codeScriptHash | count | Returns the count of outputs that have a `codeScriptHash` and contain zero photons. |
