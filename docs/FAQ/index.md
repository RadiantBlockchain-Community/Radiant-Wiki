---
hide:
  - navigation
---

# F.A.Q

## What is Radiant?

Radiant is a peer-to-peer programmable digital asset system, derived from a fork of the Bitcoin Cash (BCH) genesis block. It introduces new OPcodes that enable Turing completeness and mathematical induction proofs, effectively addressing the "back to genesis" problem. This innovative design combines the performance and parallelism advantages of an Unspent Transaction Output (UTXO) blockchain with the contract capabilities found in Ethereum-based blockchains, specifically the Ethereum Virtual Machine (EVM).

 - Powered by Proof of Work (PoW)
 - Supports Smart Contracts
 - Launched fairly
 - Utilizes a robust hashing algorithm, SHA512256d
 - Implements the ASERT DAA (Difficulty Adjustment Algorithm)
 - Genesis Date: June 20, 2022, 02:42:50 GMT+0000
 - Genesis Hash: 0000000065d8ed5d8be28d6876b3ffb660ac2a6c0ca59e437e1f7a6f4e003fb4
 - Block Time: 300 seconds (5 minutes)
 - Halving: Every 2 years (every 210,000 blocks)
 - Subsidy Emission: 50,000 per block
 - Total Coins: 21 billion, divisible into 8 decimal places

## What makes Radiant stand out from other blockchain platforms?

Radiant stands out for several reasons:

 - Induction Proofs that solve the "back-to-genesis" problem without the need for indexers.
 - Account Emulation, enabling Ethereum Virtual Machine (EVM)-like applications.
 - A Turing-complete scripting language known as RadiantScript.
 - The "0conf" feature for instant transactions.
 - A Split Node System designed for scalability.

## How was Radiant fairly launched?
Radiant was launched as a 100% PoW blockchain, ready for use, without any involvement of investors, ICOs, premining, or coin allocations. Mining guides and instructions, including how to mine with rented hash power, were publicly released by Atoshi, Radiant's developer, ensuring that everyone had equal access to RXD coins from day one.

## What is Radiant ethos? Is Radiant considered an investment? 
Radiant aims to be a scalable and flexible blockchain technology, not primarily designed for investment purposes or generating profits from RXD coin holdings. Similar to Bitcoin, which was created as a peer-to-peer electronic cash system, Radiant was designed as a peer-to-peer programmable digital asset system. While you can still acquire RXD coins from exchanges, it's essential to understand that there are no guarantees of further development. Always perform your research (DYOR) and exercise caution when considering any investment.

## Why is it a fork from BCH?
BTC civil wars divided blockchain enthusiasts into small-block and big-block proponents. In BTC, small-blocks won and big-block proponents forked it to crate BCH. BCH still ended deviating from what BTC was supposed to be based on what it is said on its' whitepaper. Another fork (BSV) was made, aiming to make it as close as possible to the original BTC proposed by Satoshi Nakamoto. This version of Bitcoin still is far from perfect as it has to rely on third party indexers to function properly. Additionally, it's associations with an individual who claims to be Satoshi Nakamoto and has initiated legal actions against some blockchain projects, raised concerns about potential legal challenges. This made BCH a more favorable choice for Radiant's development.

## What’s special about SHA512256d?
SHA512256d is a hashing algorithm that takes a SHA512 hash and truncates it to SHA256. Although SHA256 is already secure enough, SHA512256d was chosen to ensure a secure and battle-tested blockchain compatible with ASIC machines. This choice levels the playing field, fostering a truly decentralized, massively scalable, cost-efficient blockchain where no ASIC capable of mining RXD currently exists.

### When will ASICs for Radiant mining be available? 
Radiant was specifically designed to transition from GPU mining to ASIC mining for enhanced scalability and efficiency. The chosen algorithm simplifies ASIC manufacturing. However, the development of ASIC machines requires a solid economic incentive for manufacturers. It will only occur if Radiant experiences growth in its community, price, and usage demand.

## Why is the block time 5 minutes?
A 5-minute block time offers faster transaction confirmations compared to Bitcoin (BTC) while maintaining stability and security by minimizing orphan blocks. With block sizes of up to 256MB and the potential for even larger blocks, selecting the right block time is essential. In Radiant, the block time, thanks to its features, does not directly limit transactions per second or transaction speed.

### How many transactions per second can Radiant achieve?
With the current block size of 256MB, Radiant is capable of doing 1000tx/sec. If needed, miners can optimize in the future to scale linearly. For example, with 3GB blocks, 20,000tx/sec can be achieved.


## What are the benefits of Radiant’s tokenomics?
Radiant's tokenomics, with a 21 billion coin supply and 8 decimal places, positions it well for tokenizing digital and real-world assets while ensuring ample availability of RXD for both micro and macro transactions. The block time, block reward, and biyearly halvings serve as incentives attractive to miners and investors alike.


## What is the Back-To-Genesis problem?
In blockchain terms, the "back-to-genesis" problem refers to the ability to validate that all coins, transactions, and digital assets can be traced back to the genesis block. Radiant successfully solves this problem, enabling it to operate as a truly decentralized and permissionless digital asset system without relying on external third-party indexers.

## What does Turing Complete mean and why have it?
Turing completeness, in the context of a programming language, signifies that there are no limits to the types of computations that can be performed. Radiant's Turing completeness allows for highly flexible smart contract capabilities similar to those of Ethereum but without the scalability issues and high gas costs. This feature makes Radiant a unique and groundbreaking blockchain, capable of supporting a wide range of cryptocurrency and blockchain technology use cases.

### How is Radiant protected against attacks that harness turing completeness if it doesn't have a gas system like ETH?
Radiant, while Turing complete, does not incorporate loops. Loops must be unrolled to the maximum number of iterations required. This design simplifies and secures script execution, allowing fees to be calculated per byte. This discourages malicious actors from creating overly complex or inefficient scripts, as they would incur higher fees.

## What are 0conf transactions and are they safe?
A 0conf transaction is one broadcasted to the blockchain but not yet included in a block (i.e., 5 minutes have not passed). These transactions are useful and safe for microtransactions. Thanks to induction proofs, recipients of 0conf transactions can be confident that the sender indeed owns the funds being transferred. However, there is a minor risk of double-spending in such transactions. For small transactions, the potential reward for attempting a double-spending attack is typically much lower than the cost of acquiring mining hardware, electricity, and competing with the network's hash rate. In practice, it's not economically rational for an attacker to invest significant resources in double-spending small transactions. For larger transactions, it's advisable to wait for inclusion in a block, which provides increasing security with each additional block confirmation.

## How does the Split Node System work?
Radiant employs a Split Node System to accommodate its big-block design and scalability needs. Nodes in Radiant can operate in three different ways:

 - Mining Nodes: Lightweight, non-resource-intensive nodes dedicated to mining.
 - Agent Nodes: Contain essential information required for specific decentralized applications (dApps) and smart contracts.
 - Archival Nodes: Store the complete blockchain for comprehensive data access.

## Who created Radiant? 
Radiant was bootstrapped and introduced to the world by an individual or group of individuals using the pseudonym "Atoshi." Similar to the anonymous creation of Bitcoin by Satoshi Nakamoto, Attoshi delivered a fully functional product without making any promises or guarantees, allowing the community to shape its future.

## How to mine Radiant? 
Check the Radiant [mining guide](https://github.com/RadiantBlockchain/rad-bfgminer/blob/master/MINING_RAD_GUIDE.md)

## How to run a Radiant node? 
Check the Radiant node deployment guides

 - [Compile Node](https://radiant4people.com/guides/node/compile/)
 - [Run Node with Docker](https://radiant4people.com/guides/docker/radiant-node-guide/)
 - [Run Node on Flux](https://radiant-community.medium.com/host-your-electrumx-radiant-node-via-the-flux-marketplace-ecf2388832d6)

### Is there any reward for running a node? 
No, Radiant is a 100% PoW Blockchain. The only way to get RXD is through mining.

## What wallets are compatible with Radiant? 
You can access your RXD through:
### Software wallets
 - [Electron](https://github.com/RadiantBlockchain/electron-radiant/releases): an open source desktop wallet based off the popular BTC
   wallet  
 - [Chainbow](https://chainbow.io/): a mobile wallet compatible with 0conf transactions,   
   developed by the Chainbow team (recognized devs from BSV community)
 - (Soon) Photonic: a multi platform wallet developed by Radiant community   
   developers, compatible with digital asset minting
 - [Samara](https://samara.app/): a web extension wallet (currently outdated and not
   recommended)
### Hardware wallets 
Radiant will soon be integrated to [Tangem Wallet](https://tangem.com/en/help_center/supported-assets/#a5728040199453).
