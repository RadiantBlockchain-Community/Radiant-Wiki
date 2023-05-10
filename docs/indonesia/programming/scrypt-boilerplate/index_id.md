# Note: Dimodifikasi untuk menggunakan @radiantblockchian/radjs untuk testnet/superAssetR100.js

# sCrypt Project Boilerplate
[![Build Status](https://travis-ci.com/sCrypt-Inc/boilerplate.svg?branch=master)](https://travis-ci.com/sCrypt-Inc/boilerplate)
## Prasyarat
Pastikan Anda telah menginstal [sCrypt IDE](https://scrypt-ide.readthedocs.io/en/latest/index.html). **sCrypt IDE** adalah alat bagi pengembang untuk menulis, menguji, menerapkan, memanggil, dan men-debug smart contract sCrypt.

## Panduan

[**sCrypt**](https://scryptdoc.readthedocs.io) adalah bahasa pemrograman tingkat tinggi untuk menulis kontrak pintar di Bitcoin SV. Proyek ini memberikan contoh untuk membantu pengembang mempelajari dan mengintegrasikan kontrak pintar sCrypt ke proyek berbasis Javascript mereka. Prosedur yang kami rekomendasikan untuk mengembangkan aplikasi berbasis kontrak cerdas adalah sebagai berikut:

1. Pengembangan dan Pengujian Kontrak

1. Integrasi Kontrak dan Peluncuran Aplikasi

Setelah mengembangkan dan menguji unit smart contract, langkah selanjutnya adalah mengintegrasikannya ke dalam aplikasi Anda yang ditulis dalam bahasa lain seperti Javascript atau Python. Uji integrasi harus dijalankan di Bitcoin SV [Testnet](https://test.whatsonchain.com/) atau [Scaling Test Network(STN)](https://bitcoinscaling.io/) sebelum meluncurkan aplikasi ke publik pada mainnet.

## Mulai cepat
```
npm install
npm test
```

## Tata letak direktori
Untuk setiap kontrak `x`, file sumber ada di `contracts/x.scrypt`, file pengujian ada di `tests/js/x.scrypttest.js`, dan file penerapan ada di `testnet/x.js` .
<sebelum>
.
├── contracts                       # sCrypt contract files
│   ├── accumulatorMultiSig.scrypt      # <a href="https://xiaohuiliu.medium.com/accumulator-multisig-d5a5a1b5fc42">Accumulator MultiSig</a>
│   ├── ackermann.scrypt                # <a href="https://scryptdoc.readthedocs.io/en/latest/ackermann.html">Ackermann function</a>
│   ├── acs.scrypt                      # A contract which can be spent by anyone but only to a specific address
│   ├── advancedCounter.scrypt          # Use external UTXOs to pay <a href="https://medium.com/@xiaohuiliu/advanced-op-push-tx-78ce84f69a66">counter</a> contract tx fees using sighash <i>ANYONECANPAY</i>
│   ├── advancedTokenSale.scrypt        # Sambe as above, but for token sale contract
│   ├── asm.scrypt                      # Embed Script directly into sCrypt <a href="https://medium.com/@xiaohuiliu/inline-script-inside-scrypt-27d5aa279fd3">using inline assembly</a>
│   ├── auction.scrypt                  # <a href="https://xiaohuiliu.medium.com/auction-on-bitcoin-4ba2b6c18ba7">Auction on Bitcoin</a>
│   ├── binaryOption.scrypt             # <a href="https://powping.com/posts/425fe57d0d7cc11317d0e7b7d412770a11ef18c6f159d5deade78b79725833ab">A binary option contract</a>
│   ├── cltv.scrypt                     # <a href="https://xiaohuiliu.medium.com/op-push-tx-3d3d279174c1">CheckLockTimeVerify without OP_CLTV</a>
│   ├── cointoss.scrypt                 # <a href="https://xiaohuiliu.medium.com/another-fair-bitcoin-toss-742894b086cd">Fair BitCoin Toss using Blum's Protocol</a>
│   ├── cointossxor.scrypt              # <a href="https://xiaohuiliu.medium.com/fair-bitcoin-toss-7310256e60c3">Fair BitCoin Toss using XOR</a>
│   ├── conwaygol.scrypt                # <a href="https://xiaohuiliu.medium.com/play-conways-game-of-life-on-bitcoin-forever-47c6fb7ed682">Conway’s Game of Life</a>
│   ├── counter.scrypt                  # Count the number of times a function has been called to showcase <a href="https://medium.com/coinmonks/stateful-smart-contracts-on-bitcoin-sv-c24f83a0f783">stateful contract</a>
│   ├── ec.scrypt                       # <a href="https://xiaohuiliu.medium.com/efficient-elliptic-curve-point-addition-and-multiplication-in-scrypt-script-f7e143a752e2">Elliptic curve point add/multipliy</a>
│   ├── ecdsa.scrypt                    # <a href="https://xiaohuiliu.medium.com/ecdsa-signature-verification-in-script-d1e8dda5f893">ECDSA Signature Verification</a>
│   ├── demo.scrypt                     # "hello world" contract
│   ├── faucet.scrypt                   # <a href="https://blog.csdn.net/Edward_sv/article/details/109119838">rate-limited onchain faucet</a>
│   ├── forward.scrypt                  # <a href="https://xiaohuiliu.medium.com/p2p-bitcoin-settled-derivatives-forward-contracts-94d48b909b94">P2P Bitcoin-Settled Derivatives: Forward Contracts</a>
│   ├── hashpuzzlep2pkh.scrypt          # combining <a href="https://scryptdoc.readthedocs.io/en/latest/multipartyhashpuzzles.html">hash puzzle</a> and p2pkh contracts
│   ├── kaggle.scrypt                   # <a href="https://xiaohuiliu.medium.com/machine-learning-marketplace-on-bitcoin-d8eb577be812">Kaggle Competitions on Bitcoin</a>
│   ├── lottery.scrypt                  # <a href="https://xiaohuiliu.medium.com/secure-multiparty-computations-on-bitcoin-953a64843b94">lottery</a>
│   ├── mast.scrypt                     # <a href="https://xiaohuiliu.medium.com/merkelized-abstract-syntax-tree-6a49b2008435">Merklized Abstract Syntax Tree</a>
│   ├── merkleToken.scrypt              # Token based on Merkle Tree
│   ├── merkleTree.scrypt               # <a href="https://medium.com/@xiaohuiliu/scalable-state-storage-in-bsv-smart-contracts-60f9aeb3b1f">Merkle Tree</a> validation and updating
│   ├── nonFungibleToken.scrypt         # <a href="https://medium.com/@xiaohuiliu/non-fungible-tokens-on-bitcoin-sv-4575368f46a">non-fungible token</a>
│   ├── oracle.scrypt                    # <a href="https://xiaohuiliu.medium.com/ecdsa-based-oracles-on-bitcoin-e69d15afe6c5">ECDSA-based Oracles</a>
│   ├── p2pkh.scrypt                    # <a href="https://scryptdoc.readthedocs.io/en/latest/p2pkh.html">p2pkh</a> contract written in sCrypt
│   ├── p2sh.scrypt                     # <a href="https://medium.com/@xiaohuiliu/sun-rising-p2sh-7ebfca9283aa">p2sh after Genesis</a>
│   ├── perceptron.scrypt               # <a href="https://xiaohuiliu.medium.com/ai-on-bitcoin-96bbc97a62b9">AI/perceptron</a>
│   ├── perceptron2.scrypt              # <a href="https://xiaohuiliu.medium.com/how-to-train-ai-using-bitcoin-3a20ef620143">Outsource perceptron training</a>
│   ├── rabin.scrypt                    # <a href="https://medium.com/coinmonks/access-external-data-from-bitcoin-smart-contracts-2ecdc7448c43">Rabin signature</a> to import off-chain data into a contract via oracle
│   ├── recurring.scrypt                # <a href="https://xiaohuiliu.medium.com/patreon-on-bitcoin-4c3626d4ce5">Recurring Payments</a>
│   ├── rpuzzle.scrypt                  # <a href="https://wiki.bitcoinsv.io/index.php/R-Puzzles">R-Puzzle</a>
│   ├── schnorr.scrypt                  # <a href="https://xiaohuiliu.medium.com/schnorr-signatures-on-bitcoin-397ca51d8bda">Schnorr signatures</a>
│   ├── rps.scrypt                      # Rock Paper Scissors
│   ├── simpleBVM.scrypt                # <a href="https://github.com/sCrypt-Inc/boilerplate/pull/57">A simple Bitcoin Script interpreter: a Bitcoin VM inside a BVM </a>
│   ├── spvToken.scrypt                 # <a href="https://medium.com/@xiaohuiliu/peer-to-peer-tokens-6508986d9593">Peer-to-peer Tokens</a>
│   ├── statecounter.scrypt             # <a href="https://medium.com/coinmonks/introducing-stateful-properties-f0f6bce45a06">Stateful Contract</a>
│   ├── stateStruct.scrypt              # Recommended way to implement a stateful contract using struct
│   ├── sudoku.scrypt                   # <a href="https://xiaohuiliu.medium.com/sudoku-on-bitcoin-bd78551956fb">Sudoku</a>
│   ├── svd.scrypt                      # <a href="https://xiaohuiliu.medium.com/machine-learning-on-bitcoin-40f830ad1b43">Machine Learning using Singular Value Decomposition as an example</a>
│   ├── tictactoe.scrypt                # <a href="https://medium.com/@xiaohuiliu/tic-tac-toe-on-bitcoin-sv-5acdf5bd676d">TicTacToe</a> onchain p2p gaming
│   ├── timedcommit.scrypt              # <a href="https://xiaohuiliu.medium.com/bitcoin-smart-contract-2-0-d1e044abed5a">Bitcoin Smart Contract 2.0</a>: Trustless contracting by combining on-chain and off-chain transactions
│   ├── token.scrypt                    # <a href="https://medium.com/coinmonks/layer-1-tokens-on-bitcoin-sv-e78c8abf270d">Layer-1 tokens</a> by storing token map as contract state in a single UTXO
│   ├── tokenSale.scrypt                # Selling tokens for bitcoins using <a href="https://medium.com/@xiaohuiliu/atomic-swap-on-bitcoin-sv-abc28e836cd5">atomic swap</a>
│   ├── tokenSwap.scrypt                # Merkle tree-based token and bitcoin swap <a href="https://powping.com/posts/2ce8261d34d7b7745343678039e1578b86507acad30bc768b8edaf4629560d01">Token swap</a>
│   ├── tokenUtxo.scrypt                # <a href="https://medium.com/@xiaohuiliu/utxo-based-layer-1-tokens-on-bitcoin-sv-f5e86a74c1e1">fungible token</a>
│   ├── treeSig.scrypt                  # <a href="https://xiaohuiliu.medium.com/tree-signatures-8d03a8dd3077">Tree signatures</a>
│   └── util.scrypt                     # utility functions and constants
├── testnet                         # examples to deploy contract and call its function on testnet
    └── fixture
        └── autoGen                     # contract description json files
└── tests                           # contract test files
    ├── js                              # Javascript unit tests
    └── ts                              # Typescript unit tests
</pre>

## Cara menulis tes untuk kontrak sCrypt

Langkah-langkah utama untuk menulis tes sCrypt dicontohkan oleh `tests/demo.scrypttest.js`.

1. Instal dan impor/wajibkan [`scryptlib` libary](https://github.com/sCrypt-Inc/scryptlib), yang merupakan SDK javascript untuk mengintegrasikan kontrak cerdas sCrypt.

```
npm install scryptlib
```


```javascript
import { buildContractClass } from 'scryptlib';
```


2. Gunakan fungsi `buildContractClass` yang diimpor untuk mendapatkan kontrak yang tercermin, yang memiliki properti dan metode yang sama seperti yang ditentukan dalam kontrak sCrypt yang ditentukan.

```javascript
// build a contract class
// either by compiling the contract from scratch
const Demo = buildContractClass(compileContract('demo.scrypt'))
// or from contract desc file if it's already generated from compilation
const Demo = buildContractClass(loadDesc('demo_desc.json'))
```

Perhatikan bahwa `demo_desc.json` adalah nama file deskripsi dari kontrak yang dikompilasi, yang akan dihasilkan secara otomatis jika Anda menjalankan `npm run watch` dan namanya mengikuti aturan `$contractName_desc.json`.

1. Inisialisasi kontrak.

```javascript
demo = new Demo(4, 7);
```

2. Tulis pengujian untuk kontrak yang dibuat seperti yang akan Anda lakukan di Javascript.

```javascript
const result = demo.add(7 + 4).verify()
expect(result.success, result.error).to.be.true
```

## Cara menjalankan pengujian secara lokal

### Jalankan menggunakan **sCrypt IDE**
Jalankan file tes unit dalam menu konteks editor/explorer.

![Screenshot](https://scrypt-ide.readthedocs.io/en/latest/_images/run_testting.gif)

**Catatan:** File pengujian harus diakhiri dengan `.scrypttest.js` atau `.scrypttest.ts`, jika tidak, opsi "Jalankan Pengujian sCrypt" tidak akan muncul di menu.

### Jalankan dari konsol
Pengujian juga dapat dijalankan dari konsol dengan mengeksekusi `npm test`, seperti pengujian Javascript/TypeScript biasa.

## Cara menerapkan kontrak dan berinteraksi dengannya

### Men-deploy dengan menulis kode javascript/typescript

1. Berikan kunci pribadi dengan dana di `privateKey.js`
```javascript
const key = '$YOUR_PRIVATE_KEY_HERE'
```
2. Menyebarkan kontrak dan memanggil fungsinya dengan mengeluarkan
```bash
node testnet/demo.js
```
Output seperti berikut ini akan muncul di console. Dan Anda telah berhasil menerapkan kontrak dan memanggil fungsinya di Bitcoin. Manis!

```
locking txid:      8d58ff9067f5fa893b5c695179559e108ebf850d0ce4fd1e42bc872417ffd424
unlocking txid:    c60b57e93551a6c52282801130649c6a97edcca5d2b28b8b4ae2afe0ee59bf79
Succeeded on testnet
```
**sangat disarankan** untuk menguji kontrak Anda di testnet terlebih dahulu, sebelum menerapkannya di mainnet. Secara default, penerapan ada di testnet. Untuk beralih ke mainnet, cukup modifikasi `API_PREFIX` di `helper.js`.
```javascript
const API_PREFIX = 'https://api.whatsonchain.com/v1/bsv/main'
// const API_PREFIX = 'https://api.whatsonchain.com/v1/bsv/test' for Testnet
// const API_PREFIX = 'https://api.whatsonchain.com/v1/bsv/stn' for Scaling Test Net
```
Sebelum men-deploy kontrak, pastikan kontrak terbaru telah dikompilasi ke [file json deskripsi](https://github.com/scrypt-sv/scryptlib#contract-description-file), yang akan di-deploy. Ini dapat dilakukan secara otomatis dengan menjalankan proses daemon dengan perintah `npm run watch`. Ini akan memantau perubahan file kontrak dan mengkompilasi ulang bila diperlukan. Semua file deskripsi yang dihasilkan terletak di `testnet/fixture/autoGen`. Pastikan itu sesuai dengan kontrak sebelum penerapan.

### Deploy dengan menggunakan **sCrypt IDE** [fitur deploy](https://scrypt-ide.readthedocs.io/en/latest/deploy.html)

IDE menyediakan antarmuka UI universal. Anda dapat menerapkan kontrak dengan satu klik hanya dengan mengisi parameter yang relevan. Anda dapat memanggil fungsi publik dari kontrak dengan mengklik tombol tanpa menulis sebaris kode pun.

![Screenshot](https://scrypt-ide.readthedocs.io/en/latest/_images/deploy_demo.gif)