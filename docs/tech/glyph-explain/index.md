---
hide:
  - toc
---

# Introduction

This is a guide to the Glyphs protocol on the Radiant blockchain.

Glyphs is a token standard utilizing Radiant's induction proof system and advanced scripting capabilities. Each token is identified by a Radiant reference (ref) and uses smart contracts for enforcing token transfer rules. Glyphs is a fully layer one protocol with all rules enforced by miners.

The Glyphs protocol operates efficiently with minimal indexing requirements, not requiring wallets or indexers to track each token transfer back to the mint. This allows wallets to efficiently verify tokens using SPV. This makes Glyphs a more secure, efficient and scalable system than layer two alternatives.

The protocol supports both fungible and non-fungible tokens. Fungible tokens use Radiant photons as the unit of account, with each fungible token backed by one photon. NFTs may be backed by any number of Radiant photons.

Token contracts are simple, concise and extensible, allowing composition with other custom contracts to support many complex use cases.

Glyphs is currently implemented in Photonic Wallet, Glyph Miner and Orbital Wallet.

For support please join the Radiant community [Discord server](https://discord.com/invite/radiantblockchain).
