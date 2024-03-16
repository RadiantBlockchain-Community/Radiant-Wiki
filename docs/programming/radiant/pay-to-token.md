# Token-Controlled Contracts on Radiant (Pay to Token)

Token-controlled contracts revolutionize contract composition and management on Radiant, facilitating the separation of contracts into multiple UTXOs, each with distinct functionalities. With token ownership housed in its UTXO and additional UTXOs for data storage and specialized functionalities, developers can seamlessly integrate custom functionalities with standard token scripts, extending contract capabilities effortlessly.


## Script Details
**Token Input**
A standard singleton + P2PKH token script:
```
OP_PUSHINPUTREFSINGLETON <tokenRef> OP_DROP <p2pkh>
```
**Token Output**
The token output features a state script binding it to the contract input using ´OP_REQUIREINPUTREF' , followed by the contract's script signature hash:
```
OP_REQUIREINPUTREF <contractRef>
<contractScriptSigHash> OP_2DROP // Hash256 of contract's script sig
OP_STATESEPARATOR
OP_PUSHINPUTREFSINGLETON <tokenRef> OP_DROP <p2pkh> // Standard token script
```
**Script Sig**
```
<hashIndex> // Position of ref + hash in token output
<refIndex> // Index of ref in token output data summary
<outputIndex> // Output index of token
```
**Script Pub Key**
```
// Contract ref
OP_PUSHINPUTREFSINGLETON <contractRef> OP_DROP
// Check token ref exists in the token output
OP_DUP OP_ROT OP_REFDATASUMMARY_OUTPUT OP_SWAP 36 OP_MUL OP_SPLIT OP_NIP 32 OP_SPLIT OP_DROP <tokenRef> OP_DUP OP_ROT OP_EQUALVERIFY
// Get ref + hash in token output script
OP_ROT OP_STATESCRIPTBYTECODE_OUTPUT OP_SWAP OP_SPLIT OP_NIP 69 OP_SPLIT OP_DROP
// Compare ref + hash in token output to ref + hash of this script's unlocking code
OP_SWAP 32 OP_INPUTINDEX OP_INPUTBYTECODE OP_HASH256 OP_CAT OP_CAT OP_EQUALVERIFY
// Propagate the contract
OP_INPUTINDEX OP_CODESCRIPTBYTECODE_UTXO OP_HASH256 OP_CODESCRIPTHASHOUTPUTCOUNT_OUTPUTS 1 OP_NUMEQUALVERIFY
//
// Contract code...
// 
```
This script empowers complex contracts composed of multiple UTXOs, each with unique responsibilities. Token scripts maintain standard scripts easily understood by wallets but can be associated with custom contracts. Pay to token contracts can be deployed at any time to expand token functionality.

## Advantages

- Enhanced Functionality: Contracts can be extended with custom functionalities effortlessly.
- Streamlined Updates: Token transfers and data updates are segregated, simplifying history tracking.
- Reduced Load: Wallets can focus on relevant transaction histories, improving efficiency.
- This script can be used to break a contract up into multiple parts. For example a complex NFT contract, split into a token contract and ownership contract:

Token-controlled contracts pave the way for more flexible and manageable blockchain contracts, fostering innovation and scalability in decentralized applications.
