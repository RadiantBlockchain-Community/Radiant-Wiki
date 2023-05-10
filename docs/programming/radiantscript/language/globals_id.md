---
title: Variabel Global
---

## Unit yang tersedia secara global
Literal bilangan bulat dapat menggunakan sufiks unit moneter atau sementara untuk menambahkan nilai semantik ke bilangan bulat ini dan untuk menyederhanakan aritmatika. Saat satuan ini digunakan, bilangan bulat yang mendasarinya secara otomatis dikalikan dengan nilai satuan. Unit `sats`, `finney`, `bits` dan `bitcoin` digunakan untuk menunjukkan nilai moneter, sedangkan unit `seconds`, `minutes`, `hours`, `days` dan `weeks` digunakan untuk menunjukkan waktu.

:::peringatan
Berhati-hatilah saat menggunakan satuan ini dalam perhitungan kalender yang tepat, karena tidak setiap tahun sama dengan 365 hari dan bahkan tidak setiap menit memiliki 60 detik karena [detik kabisat](https://en.wikipedia.org/wiki/Leap_second).
:::

### Contohnya
```solidity
require(1 sats == 1);
require(1 finney == 10);
require(1 bit == 100);
require(1 bitcoin == 1e8);

require(1 seconds == 1);
require(1 minutes == 60 seconds);
require(1 hours == 60 minutes);
require(1 days == 24 hours);
require(1 weeks == 7 days);
```

## Variabel kunci waktu global
Bitcoin Cash memiliki dukungan untuk berbagai jenis kunci waktu, yang dapat digunakan untuk menentukan kondisi berbasis waktu di dalam kontrak Bitcoin Cash. Kunci waktu ini dapat dipisahkan oleh **tiga atribut utama**: lokasi, penargetan, dan metrik. *Lokasi* dapat berupa *tingkat transaksi* atau *tingkat kontrak*, tetapi kunci *tingkat kontrak* juga mengharuskan Anda untuk menggunakan kunci *tingkat transaksi* yang sesuai. Penargetannya bisa *relatif* (mis. setidaknya 4 jam telah berlalu) atau *absolut* (mis. nomor blok setidaknya 600.000). Metrik dapat berupa blok atau detik.

Sulit untuk sepenuhnya memahami seluk-beluk kunci waktu, jadi jika Anda baru memulai, disarankan untuk memulai dengan versi yang paling sederhana: kunci waktu berbasis blok absolut. Jika Anda ingin mempelajari penggunaan kunci waktu yang lebih canggih, James Prestwich menulis [**artikel terbaik** yang menjelaskan kunci waktu dalam Bitcoin](https://prestwi.ch/bitcoin-time-locks/) yang juga sepenuhnya berlaku untuk Bitcoin Cash.


### tx.waktu
`tx.time` digunakan untuk membuat kunci waktu *absolut*. Nilai `tx.time` dapat mewakili nomor blok transaksi pembelanjaan atau stempel waktunya. Saat membandingkannya dengan nilai di bawah `500.000.000`, ini diperlakukan sebagai nomor blok, sedangkan nilai yang lebih tinggi diperlakukan sebagai stempel waktu.

Karena keterbatasan Skrip Bitcoin yang mendasarinya, `tx.time` hanya dapat digunakan dengan cara berikut:

```solidity
require(tx.time >= <expression>);
```

karena cara kerja kunci waktu, **kunci waktu yang sesuai perlu ditambahkan ke transaksi**. CashScript SDK secara otomatis menyetel kunci waktu *tingkat transaksi* ini ke nomor blok terbaru, karena ini adalah kasus penggunaan yang paling umum. Jika Anda perlu menggunakan nomor blok atau stempel waktu yang berbeda, ini harus diteruskan ke CashScript SDK menggunakan fungsi [`withTime()`][withTime()]. Jika default cocok dengan kasus penggunaan Anda, **tidak diperlukan tindakan tambahan**.

:::catatan
`tx.time` sesuai dengan kolom `nLocktime` dari transaksi saat ini dan opcode `OP_CHECKLOCKTIMEVERIFY`.
:::

### tx.usia
`tx.age` digunakan untuk membuat kunci waktu *relatif*. Nilai `tx.age` dapat mewakili sejumlah blok, atau sejumlah *potongan*, yaitu 512 detik. Kunci waktu *tingkat transaksi* yang sesuai menentukan mana dari dua opsi yang digunakan.

Karena keterbatasan Skrip Bitcoin yang mendasarinya, `tx.age` hanya dapat digunakan dengan cara berikut:

```solidity
require(tx.age >= <expression>);
```

Karena cara kerja kunci waktu, **kunci waktu yang sesuai perlu ditambahkan ke transaksi**. Hal ini dapat dilakukan di CashScript SDK menggunakan fungsi [`withAge()`][withAge()]. Namun, nilai yang diteruskan ke fungsi ini akan selalu diperlakukan sebagai sejumlah blok, jadi **saat ini tidak didukung untuk menggunakan `tx.age` sebagai sejumlah potongan kedua**.

:::catatan
`tx.age` sesuai dengan kolom `nSequence` dari *UTXO* saat ini dan opcode `OP_CHECKSEQUENCEVERIFY`.
:::

## Variabel introspeksi
Fungsionalitas introspeksi digunakan untuk membuat kontrak *perjanjian*. Perjanjian adalah teknik yang digunakan untuk membatasi pengeluaran uang di dalam kontrak pintar. Kasus penggunaan utama dari ini adalah membatasi alamat di mana uang dapat dikirim dan jumlah yang dikirim. Untuk mengeksplorasi kemungkinan penggunaan perjanjian di dalam kontrak pintar, baca [Panduan Perjanjian CashScript][panduan-perjanjian].

### this.activeInputIndex
```solidity
int this.activeInputIndex
```

Selama validasi transaksi BCH, setiap input transaksi dievaluasi secara berurutan, dan kode kontrak dievaluasi dalam konteks input yang berbeda. `this.activeInputIndex` mewakili indeks input yang saat ini sedang dievaluasi. Ini dapat digunakan bersamaan dengan properti di bawah `tx.inputs`.

### this.activeBytecode
```solidity
bytes this.activeBytecode
```

Selama validasi transaksi BCH, setiap input transaksi dievaluasi secara berurutan, dan kode kontrak dievaluasi dalam konteks input yang berbeda. `this.activeBytecode` mewakili bytecode kontrak dari input yang saat ini sedang dievaluasi.

### tx.version
```solidity
int tx.version
```

Mewakili versi transaksi saat ini. Versi transaksi yang berbeda dapat memiliki perbedaan fungsi. Saat ini hanya ada versi 1 dan 2, dimana hanya versi 2 yang mendukung [BIP68][bip68].

### tx.locktime
```solidity
int tx.locktime
```

Rmewakili bidang `nLocktime` dari transaksi.

:::catatan
`tx.locktime` mirip dengan variabel global [`tx.time`][tx.time]. Direkomendasikan untuk hanya menggunakan `tx.locktime` untuk menambahkan `nLocktime` ke keadaan simulasi dan [`tx.time`][tx.time] di semua kasus lainnya.
:::

### tx.inputs
Merupakan daftar input dari transaksi yang dievaluasi. Ini adalah array, dan tidak dapat digunakan sendiri. Anda perlu mengakses input dengan indeks tertentu dan menentukan properti yang ingin Anda akses.

#### tx.inputs.length
```solidity
int tx.inputs.length
```

Mewakili jumlah input dalam transaksi.

#### tx.inputs[i].value
```solidity
int tx.inputs[i].value
```

Mewakili nilai masukan tertentu (dalam satoshi).

#### tx.inputs[i].lockingBytecode
```solidity
bytes tx.inputs[i].lockingBytecode
```

Mewakili bytecode penguncian (`scriptPubKey`) dari input tertentu.

#### tx.inputs[i].unlockingBytecode
```solidity
bytes tx.inputs[i].unlockingBytecode
```

Mewakili bytecode pembuka kunci (`scriptSig`) dari input tertentu.

#### tx.inputs[i].outpointTransactionHash
```solidity
bytes32 tx.inputs[i].outpointTransactionHash
```

Mewakili hash transaksi outpoint di mana input tertentu awalnya dikunci.

#### tx.inputs[i].outpointIndex
```solidity
int tx.inputs[i].outpointIndex
```

Mewakili indeks outpoint tempat input tertentu awalnya dikunci.

#### tx.inputs[i].sequenceNumber
```solidity
int tx.inputs[i].sequenceNumber
```

Mewakili nomor `nSequence` dari input tertentu.

### tx.outputs
Merupakan daftar output dari transaksi yang dievaluasi. Ini adalah array, dan tidak dapat digunakan sendiri. Anda perlu mengakses output dengan indeks tertentu dan menentukan properti yang ingin Anda akses.

#### tx.outputs.length
```solidity
int tx.outputs.length
```

Mewakili jumlah output dalam transaksi.

#### tx.outputs[i].value
```solidity
int tx.outputs[i].value
```

Mewakili nilai output tertentu (dalam satoshi).

#### tx.outputs[i].lockingBytecode
```solidity
bytes tx.outputs[i].lockingBytecode
```

Mewakili bytecode penguncian (`scriptPubKey`) dari output tertentu.

## Membangun bytecode penguncian
Salah satu kasus penggunaan utama dari perjanjian adalah menegakkan hasil transaksi (di mana uang dikirim). Untuk membantu menerapkan output ini, ada sejumlah objek `LockingBytecode` yang dapat dibuat instance-nya. Bytecode penguncian ini kemudian dapat dibandingkan dengan bytecode penguncian output transaksi.

#### Contoh
```solidity
bytes25 lockingBytecode = new LockingBytecodeP2PKH(pkh);
int value = 10000;
require(tx.outputs[0].lockingBytecode == lockingBytecode);
require(tx.outputs[0].value == value);
```

### LockingBytecodeP2PKH
```solidity
new LockingBytecodeP2PKH(bytes20 pkh): bytes25
```

Membuat bytecode penguncian P2PKH baru untuk hash kunci publik `pkh`.

### LockingBytecodeP2SH
```solidity
new LockingBytecodeP2SH(bytes20 scriptHash): bytes23
```

Membuat bytecode penguncian P2SH baru untuk hash skrip `scriptHash`.

### LockingBytecodeNullData
```solidity
new LockingBytecodeNullData(bytes[] chunks): bytes
```

Membuat bytecode penguncian OP_RETURN baru dengan `chunks` sebagai data OP_RETURN-nya.

[bip68]: https://github.com/bitcoin/bips/blob/master/bip-0068.mediawiki
[withAge()]: /docs/sdk/transactions#withage
[withTime()]: /docs/sdk/transactions#withtime
[covenants-guide]: /docs/guides/covenants
[tx.time]: #txtime
