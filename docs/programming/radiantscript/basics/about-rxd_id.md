---
title: Apa itu Radiant?
sidebar_label: Tentang Radiant
---

:::peringatan
Ini adalah halaman dalam proses, ini akan ditambahkan ke struktur situs di kemudian hari. Jika Anda masih sampai di halaman ini, jangan ragu untuk membacanya, tetapi perhatikan bahwa ini sedang dalam proses.
:::

Radiant (atau RXD) adalah sistem kas elektronik peer-to-peer. Ini menggunakan *blockchain* untuk mendistribusikan ledger-nya melalui jaringan node independen sehingga tidak ada titik kegagalan tunggal, dan tidak ada kontrol pusat yang mungkin disusupi. Ini menggunakan algoritme konsensus yang disebut *Proof-of-Work* yang memungkinkan node independen ini untuk menyetujui transaksi yang benar dan menolak yang berbahaya.

## Dasar
*Blockchain* adalah struktur data yang didistribusikan ke sejumlah node independen. Itu mendapatkan namanya dari rantai * blok * yang digunakannya untuk menyimpan datanya. Semua blok menyertakan *block header* dengan beberapa metadata dan root dari *Merkle tree* - jenis pohon khusus yang memungkinkan validasi data dengan cepat. Pohon Merkle ini kemudian digunakan untuk menyimpan data aktual di dalam blok ini. Untuk membuat rantai tahan terhadap
manipulasi, header blok juga menyertakan stempel waktu dan hash dari blok sebelumnya.

### Proof-of-Work
Radiant dan banyak blockchain publik lainnya menggunakan algoritma konsensus yang disebut *Proof-of-Work (PoW)*. Algoritme ini bekerja dengan melampirkan sebuah nonce ke setiap header blok dan mengubah nonce ini hingga hash dari header blok cocok dengan awalan tertentu. Proses ini disebut penambangan, dan dicoba oleh banyak node secara bersamaan, sampai salah satu dari mereka menemukan solusi yang tepat. Salah satu atribut dari algoritma ini adalah penambangan sangat mahal, tetapi node lain dapat memverifikasi solusinya dengan sangat cepat.

Penambangan juga merupakan proses di mana koin baru diperkenalkan ke total pasokan moneter. Penambang memvalidasi transaksi dan mengamankan jaringan, di mana mereka dibayar dengan koin baru - disebut *hadiah blok* - dalam transaksi khusus yang disebut transaksi *coinbase*. Tingginya biaya proses penambangan menambah risiko keuangan untuk salah memvalidasi transaksi. Pada saat yang sama, hadiah blok melampirkan hadiah finansial untuk memvalidasi transaksi dengan benar. Proses ini memastikan bahwa node yang saling tidak percaya dapat berkolaborasi untuk memvalidasi transaksi.
### Transaksi
Transaksi bercahaya dibuat menggunakan potongan RXD yang disebut output transaksi. Ketika output ini tersedia, mereka disebut *Output Transaksi yang Tidak Terpakai (UTXOs)*. UTXO dikunci menggunakan skrip penguncian (atau `scriptPubKey`) yang menentukan kondisi untuk menggunakan UTXO. Saat mencoba menghabiskan UTXO, skrip pembuka kunci (atau `scriptSig`) disediakan. Skrip-skrip ini kemudian dieksekusi bersama dan transaksi hanya valid jika skrip dijalankan tanpa error dan nilai yang dihasilkan adalah `TRUE`.

Pola skrip penguncian/pembukaan kunci yang paling banyak digunakan disebut *Pay-to-Public-Key-Hash (P2PKH)*, di mana skrip penguncian berisi hash kunci publik dan mengharapkan skrip pembuka kunci berisi kunci publik dan tanda tangan transaksi. Skrip penguncian kemudian memeriksa apakah kunci publik yang diberikan cocok dengan hash yang disimpan, dan apakah tanda tangan transaksi itu valid. Pola ini digunakan di dompet Radiant biasa. Dan saldo pengguna hanyalah jumlah dari semua UTXO yang dapat digunakan oleh kunci publik pengguna.

UTXO digunakan sebagai input untuk transaksi Radiant dan menghasilkan UTXO baru sebagai output. UTXO harus dibelanjakan seluruhnya dalam transaksi. Jadi, kapan pun pengguna ingin menggunakan 10 RXD UTXO untuk mengirim 1 RXD kepada seseorang, mereka harus mengirim 9 RXD kembali ke dirinya sendiri. Realistis, bagian dari dana akan disediakan untuk biaya transaksi juga.

## Smart Contracts
Uang elektronik peer-to-peer adalah kasus penggunaan nyata pertama dari teknologi blockchain. Namun dalam beberapa tahun terakhir, kontrak pintar semakin populer. Kontrak pintar ini memungkinkan orang untuk menggunakan keamanan yang ditawarkan oleh blockchain seperti Bitcoin, Radiant, dan Ethereum dan menerapkannya untuk menggunakan kasing selain uang tunai. Terutama aplikasi Decentralized Finance (DeFi) seperti [Maker](https://makerdao.com/), [Uniswap](https://uniswap.org/) dan [Aave](https://aave.com/) telah meroket.

Sebagian besar inovasi kontrak pintar telah terjadi di Ethereum, tetapi platform lain seperti Bitcoin dan Radiant juga memiliki beberapa dukungan untuk kontrak pintar. Kontrak pintar di setiap platform bekerja secara berbeda, dan perbedaan utama antara kontrak pintar di Ethereum dan Radiant adalah bahwa kontrak pintar di Ethereum bersifat stateful, sedangkan kontrak pintar di Radiant tidak memiliki kewarganegaraan.

Ini berarti bahwa kontrak Ethereum dapat merekam dan memperbarui variabel, sedangkan variabel dalam kontrak Radiant tidak dapat diubah.

### Script Radiant
Skrip penguncian dan pembukaan kunci transaksi reguler dan kontrak pintar di Radiant ditulis menggunakan bahasa skrip transaksi Radiant, yang diberi nama kreatif Script. Untuk menghindari ambiguitas, ini juga bisa disebut sebagai Bitcoin Script atau Radiant Script. Script adalah bahasa seperti rakitan berbasis tumpukan yang sengaja tidak selesai karena penggunaan utamanya adalah validasi uang yang dapat diprogram, bukan komputasi tujuan umum.

Skrip tidak memiliki kewarganegaraan, artinya hanya menggunakan informasi yang terkandung di dalam skrip penguncian dan pembukaan kunci itu sendiri. Keadaan tanpa kewarganegaraan ini berarti bahwa sebuah Script dapat divalidasi secara deterministik pada mesin apa pun. Ini memberikan peningkatan kinerja dan prediktabilitas, meskipun itu membatasi kegunaan bahasa scripting.

### Kontrak Radiant

MELAKUKAN