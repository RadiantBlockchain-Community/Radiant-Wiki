---
title: Permulaan
---

## Menginstal kompiler CashScript
baris perintah Compiler CashScript  `cashc` dapat diinstal dari NPM.

```bash
npm install -g cashc
```

## Memasang JavaScript SDK
SDK JavaScript dapat dipasang ke proyek Anda dengan NPM.

```bash
npm install cashscript
```

:::peringatan
CashScript hanya menawarkan SDK JavaScript, tetapi kontrak CashScript juga dapat diintegrasikan ke dalam bahasa lain. Karena tidak ada SDK siap pakai yang tersedia untuk mereka, ini dianggap sebagai penggunaan tingkat lanjut, dan disarankan untuk menggunakan JavaScript SDK.
:::

## Menulis kontrak cerdas pertama Anda
Ada beberapa contoh yang tersedia di [Contoh halaman](/id/docs/language/examples), yang dapat digunakan untuk mengambil inspirasi. Contoh lebih lanjut dari integrasi JavaScript dapat ditemukan di [GitHub](https://github.com/Bitcoin-com/cashscript/tree/master/examples). Contoh sederhana disertakan di bawah ini.

```solidity
pragma cashscript ^0.7.0;

contract TransferWithTimeout(pubkey sender, pubkey recipient, int timeout) {
    // Allow the recipient to claim their received money
    function transfer(sig recipientSig) {
        require(checkSig(recipientSig, recipient));
    }

    // Allow the sender to reclaim their sent money after the timeout is reached
    function timeout(sig senderSig) {
        require(checkSig(senderSig, sender));
        require(tx.time >= timeout);
    }
}
```

:::tip
Baca selengkapnya tentang sintaks bahasa CashScript di [Deskripsi Bahasa](/docs/language/contracts).
:::

## Mengintegrasikan ke dalam JavaScript
Sementara contoh yang lebih mendetail tersedia di GitHub, kami menunjukkan integrasi kontrak `TransferWithTimeout` dalam proyek JavaScript.

Setelah mengkompilasi file kontrak ke JSON artefak dengan cashc, itu dapat diimpor ke SDK CashScript.

```bash
cashc ./transfer_with_timeout.cash --output ./transfer_with_timeout.json
```

```javascript
const { ElectrumNetworkProvider, Contract, SignatureTemplate } = require('cashscript');
const { alice, bob, alicePk, bobPk } = require('./keys');

async function run() {
  // Import the TransferWithTimeout JSON artifact
  const artifact = require('./transfer_with_timeout.json');

  // Initialise a network provider for network operations
  const provider = new ElectrumNetworkProvider('mainnet');

  // Instantiate a new TransferWithTimeout contract
  const contract = new Contract(artifact, [alicePk, bobPk, 600000], provider);

  // Call the transfer function with Bob's signature
  // i.e. Bob claims the money that Alice has sent him
  const transferDetails = await contract.functions
    .transfer(new SignatureTemplate(bob))
    .to('bitcoincash:qrhea03074073ff3zv9whh0nggxc7k03ssh8jv9mkx', 10000)
    .send();
  console.log(transferDetails);

  // Call the timeout function with Alice's signature
  // i.e. Alice recovers the money that Bob has not claimed
  const timeoutDetails = await contract.functions
    .timeout(new SignatureTemplate(alice))
    .to('bitcoincash:qqeht8vnwag20yv8dvtcrd4ujx09fwxwsqqqw93w88', 10000)
    .send();
  console.log(timeoutDetails);
}
```

:::tip
Baca selengkapnya tentang JavaScript SDK di [dokumentasi SDK](/docs/sdk/instantiation).
:::
