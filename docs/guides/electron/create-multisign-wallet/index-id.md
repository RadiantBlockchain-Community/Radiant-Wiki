---
hide:
  - toc
---


**Buat Multi-tanda tangan dengan 3 dompet dan 2 membutuhkan penanda tangan (3 dari 2)**

Salah satu fitur baru **ZEUS** dan Electron Wallet 0.1.3 adalah kemampuan untuk menggunakan dompet multi-tanda tangan. Berikut adalah proses dasar penggunaannya.

## Apa multi-tanda tangan dompet itu? 
- [https://www.bitstamp.net/learn/security/what-is-a-multisig-wallet](https://www.bitstamp.net/learn/security/what-is-a-multisig-wallet)

- [https://www.coindesk.com/tech/2020/11/10/multisignature-wallets-can-keep-your-coins-safer-if-you-use-them-right](https://www.coindesk.com/tech/2020/11/10/multisignature-wallets-can-keep-your-coins-safer-if-you-use-them-right)


## Memulai

#### Diperlukan
* Versi terakhir dari Dompet Elektron. Disini saya menggunakan versi 0.1.3
* SANGAT kompatibel dengan dompet: Wndows, Linux dan Mac.
* Dompet lain atau orang lain untuk ditandatangani

#### Buat dompet.
Langkah pertama adalah membuat dompet baru. Dalam panduan ini, pengujian akan dilakukan dengan 3 dompet yang hanya perlu ditandatangani dengan dua dompet.

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/01-create-wallet.png?raw=true)

Pilih Dompet multi-tanda tangan

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/02-select-multisign-wallet.png?raw=true)

Di bagian ini adalah tempat Anda memilih jenis dompet multi-tanda tangan. Dimungkinkan untuk membuat beberapa varian yang memungkinkan, tetapi di sini kami akan melakukan 2 dari 3 seperti biasa.

- Jumlah pemberi kode: 3
- Memerlukan tanda tangan: 2
 
![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/03-select-number-cosigners-and-signatures.png?raw=true)

Sebagai contoh saya akan membuat 3 dompet dengan 3 benih baru

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/04-new-seed-restore-or-priv-key.png?raw=true)

Sangat penting untuk membuat salinan kata yang aman atau dana di dompet yang dihasilkan bisa hilang.

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/05-new-seed.png?raw=true)

Kunci publik master adalah apa yang perlu Anda tambahkan ke dompet lain untuk membentuk dompet multi-tanda tangan.

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/06-master-pub-key.png?raw=true)

Saat menambahkan cosigner, dua dompet lainnya harus memberi tahu Anda kunci publik master mereka untuk terus membuat dompet multisign. 

**PENTING: Jangan pernah memberikan seed kepada siapa pun, hanya kunci publik master.**

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/07-add-other-cosigner-master-pub-key.png?raw=true)

Buat kata sandi yang bagus untuk mengenkripsi dompet Anda

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/08-password-wallet.png?raw=true)


##Contoh yang digunakan dalam panduan
**Ini adalah data dompet yang saya buat untuk panduan dan dapat digunakan untuk meninjaunya**

- **SEED 1:** tumble cage van bind device party shoe rail valid canal witness dial
 
**masterpub-key:** 
``xpub6CCA12nJkoBPVtZUswdEnVT9LR87zxutoKpSpA4crx9khyuupX4qsuWyswbZub63Dqhz7hRYfRZt4oPd6D9yherrjUYcmQmdguas99FcW5x``
 
- **SEED 2:** avocado tuition asthma wine solar garbage limb play example hybrid unaware loud

**masterpub-key:** 
``xpub6D2CVbotwBsk7czNJQjKKWWa993PjTBJHG9fHV54yXoiPVPCZZwp6JNYUFD2PJQWfUAgjqueVwwx8nqfspGHYfmRj6FN1LCNz3wmK3hGiNR``
 
- **SEED 3:** drift kitten flash alter update program island slide leisure youth spider diet
  
**masterpub-key:** 
``xpub6DJgWZhSCwU8VWQxnzpUky18gH9ZtvesYac2rVsbuyyeDyVsBxdgXF3GUDiic73eFsf5ioZTgwKRwyv4HLACjmnKQS3Pn4j9zhWFce3hmiG``

Ketiga dompet akan terlihat seperti yang ditunjukkan pada gambar. Alamat semua dompet multi-tanda tangan dimulai dengan 3, ini benar.

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/09-same-address-all-wallets.png?raw=true)

#### Kirim RXD untuk menguji dompet

Saya telah mengirim 10,5 RXD untuk pengujian ke alamat tersebut: ``3Be9yJ3qBuHVwEsCbptmxVykRhQcXwThC4``
[https://radiantexplorer.com/address/3Be9yJ3qBuHVwEsCbptmxVykRhQcXwThC4](https://radiantexplorer.com/address/3Be9yJ3qBuHVwEsCbptmxVykRhQcXwThC4)

Saat menerima dana pada arah pertama, semua dompet menampilkan pesan untuk menerima dana pada waktu yang bersamaan.

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/10-all-wallets-recive-same-time.png?raw=true)

Dan di sini Anda akan melihatnya di dompet

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/11-all-wallets-recive-same-time.png?raw=true)

#### Uji dompet multi-tanda tangan

Sekarang saatnya proses pengiriman dengan tanda tangan dua dompet. Alamat tujuan dipilih dan jumlah yang akan dikirim. Bagian ini tidak berubah.

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/12-send-with-one-wallet.png?raw=true)

Perubahan dilakukan saat Anda mengklik kirim dan sebuah jendela muncul dengan data transaksi. Anda dapat melihat bahwa ada dua tanda tangan dan telah ditransfer ke jaringan.

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/13-transaction-broadcast.png?raw=true)

Untuk tanda tangan di dompet lain, perlu menyalin transaksi seperti yang ditunjukkan pada gambar di bawah ini

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/14-copy-tx-to-other-signer.png?raw=true)

Dan di dompet salah satu dari dua dompet lainnya pergi ke: **TOOLS > LOAD TRANSACTION > FROM TEXT**

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/15-past-tx-in-other-wallet.png?raw=true)

Di sini ditempelkan transaksi yang disalin dari dompet yang menghasilkan pengiriman

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/16-load-tx-in-other-wallet.png?raw=true)

Setelah diterima, sebuah jendela dengan transaksi akan muncul. Periksa apakah semuanya baik-baik saja dan kemudian klik **Sign** dan **Broadcast** untuk mengirim ke jaringan.

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/17-sign-and-broadcast-transaction.png?raw=true)

Jika semuanya berjalan dengan baik, jendela dengan TX yang dihasilkan akan ditampilkan

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/18-payment-sent.png?raw=true)

Dan di semua dompet Anda akan dapat melihat bagaimana dana masuk ke alamat yang dipilih.

TX: [https://radiantexplorer.com/tx/d960b5588af7302d689b00f226db4bf2e87f348b490764b7e2034479dd6d08bf](https://radiantexplorer.com/tx/d960b5588af7302d689b00f226db4bf2e87f348b490764b7e2034479dd6d08bf)

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/19-tx-final-all-wallets.png?raw=true)

Dalam informasi dompet muncul data dompet yang terlibat dalam multi-signature.

![](https://github.com/Antares-RXD/Radiant-Guides/blob/main/Create-multisign-wallet/img/20-info-all-wallets.png?raw=true)

#### Sirip
Dan inilah proses multisigning di Radiant dari Electron-Wallet 0.1.3

Antares
