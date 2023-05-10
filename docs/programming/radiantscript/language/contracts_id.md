---
title: Struktur Kontrak
---

Kontrak di CashScript agak mirip dengan kelas dalam bahasa berorientasi objek. Perbedaan penting adalah bahwa tidak ada keadaan yang bisa berubah. Jadi, setelah kontrak dibuat dengan parameter tertentu, nilai ini tidak dapat diubah. Sebaliknya, fungsi dapat dipanggil pada kontrak yang bertindak berdasarkan nilai kontrak untuk membelanjakan uang dari kontrak. Ekstensi file kode sumber CashScript adalah `.cash`, dan struktur file sumber ini dijelaskan di bawah.

## Pragma
File kontrak dapat dimulai dengan arahan pragma untuk menunjukkan versi CashScript yang digunakan untuk kontrak tersebut. Ini memastikan bahwa kontrak tidak dikompilasi dengan versi kompiler yang tidak didukung, yang dapat menyebabkan efek samping yang tidak diinginkan.

:::catatan
Arahan pragma mengikuti [aturan pembuatan versi semantik](https://semver.npmjs.com/) reguler.
:::

#### Contoh
```solidity
pragma cashscript ^0.7.0;
pragma cashscript >= 0.4.0 < 0.5.4;
```

## Konstruktor
Konstruktor CashScript bekerja sedikit berbeda dari yang biasa Anda gunakan dalam bahasa berorientasi objek biasa. Tidak mungkin mendefinisikan pernyataan apa pun di dalam konstruktor, karena konstruktor hanya digunakan untuk menyimpan nilai dalam kontrak. Karena sifatnya yang terbatas ini, tidak ada fungsi `konstruktor` yang terpisah, melainkan parameternya ditentukan langsung pada definisi kelas.

#### Contoh
```solidity
pragma cashscript ^0.7.0;

contract HTLC(pubkey sender, pubkey recipient, int expiration, bytes32 hash) {
    ...
}
```

## Fungsi
Konstruk utama dalam kontrak CashScript adalah fungsinya. Kontrak dapat berisi satu atau beberapa fungsi yang dapat dijalankan untuk memicu transaksi yang menghabiskan uang dari kontrak. Pada dasarnya, hasil dari suatu fungsi hanyalah jawaban ya atau tidak untuk pertanyaan 'Dapatkah uang dikirim dari kontrak ini?'. Namun dengan menggunakan teknik yang disebut perjanjian, dimungkinkan untuk menentukan kondisi lain, seperti membatasi *kemana* uang dapat dikirim. Untuk membaca lebih lanjut tentang teknik ini, lihat [Panduan Perjanjian CashScript](/docs/guides/covenants).

#### Contoh
```solidity
pragma cashscript ^0.7.0;

contract TransferWithTimeout(pubkey sender, pubkey recipient, int timeout) {
    function transfer(sig recipientSig) {
        ...
    }

    function timeout(sig senderSig) {
        ...
    }
}
```

## Pernyataan
Fungsi CashScript terdiri dari kumpulan pernyataan yang menentukan apakah uang dapat dibelanjakan dari kontrak.

### memerlukan()
Pernyataan paling penting dari kontrak CashScript adalah pernyataan `require`. Pernyataan ini menggunakan ekspresi boolean dan memeriksa apakah ekspresi tersebut bernilai `true`. Jika malah mengevaluasi ke `false`, transaksi gagal. Pernyataan ini digunakan untuk memastikan bahwa persyaratan dipenuhi untuk mengeluarkan uang dari kontrak.

#### Contoh
```solidity
pragma cashscript ^0.7.0;

contract P2PKH(bytes20 pkh) {
    function spend(pubkey pk, sig s) {
        require(hash160(pk) == pkh);
        require(checkSig(s, pk));
    }
}
```

### Deklarasi variabel
Variabel dapat dideklarasikan dengan menentukan jenis dan namanya. Semua variabel harus diinisialisasi pada saat deklarasinya, tetapi dapat dipindahkan nanti - kecuali menentukan kata kunci `constant`. Karena CashScript diketik dengan kuat dan tidak memiliki inferensi tipe, tidak mungkin menggunakan kata kunci seperti `var` atau `let` untuk mendeklarasikan variabel.

:::peringatan
CashScript melarang variabel membayangi dan variabel yang tidak digunakan.
:::

#### Contoh
```solidity
int myNumber = 3000;
string constant myString = 'Bitcoin Cash';
```

### Penetapan variabel
Setelah deklarasi awal mereka, variabel apa pun dapat dipindahkan nanti. Namun, CashScript tidak memiliki operator penugasan majemuk seperti `+=` atau `-=`.

#### Contoh
```solidity
i = i + 1;
hashedValue = sha256(hashedValue);
myString = 'Cash';
```

### Struktur kontrol
Satu-satunya struktur kontrol di CashScript adalah pernyataan `if` dan `else`. Hal ini disebabkan oleh keterbatasan Skrip Bitcoin yang mendasarinya yang mencegah loop, rekursi, dan pernyataan `return`. Pernyataan if-else mengikuti semantik biasa yang dikenal dari bahasa seperti C atau JavaScript.

:::catatan
Tidak ada konversi tipe implisit dari tipe non-boolean ke boolean. Jadi `if (1) { ... }` bukan CashScript yang valid dan harus ditulis sebagai `if (bool(1)) { ... }`
:::

#### Contoh
```solidity
pragma cashscript ^0.7.0;

contract OneOfTwo(bytes20 pkh1, bytes32 hash1, bytes20 pkh2, bytes32 hash2) {
    function spend(pubkey pk, sig s, bytes message) {
        require(checkSig(s, pk));
        bytes20 pkh = hash160(pk);

        if (pkh == pkh1) {
            require(sha256(message) == hash1);
        } else if (pkh == pkh2) {
            require(sha256(message) == hash2);
        } else {
            require(false); // fail
        }
    }
}
```

## Komentar
Komentar dapat ditambahkan di mana saja di file kontrak. Semantik komentar mirip dengan bahasa seperti JavaScript atau C. Artinya, komentar satu baris dapat ditambahkan dengan `// ...`, sementara komentar multibaris dapat ditambahkan dengan `/* ... */`.

#### Contoh
```solidity
// This is a single-line comment.

/*
This is a
multi-line comment.
*/
```
