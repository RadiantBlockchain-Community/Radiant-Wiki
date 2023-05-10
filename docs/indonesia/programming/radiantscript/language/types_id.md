---
title: Jenis
---

CashScript adalah bahasa yang diketik secara statis, yang berarti bahwa jenis setiap variabel perlu ditentukan. Tipe juga dapat secara implisit atau eksplisit dilemparkan ke tipe lain. Untuk referensi singkat berbagai kemungkinan casting, lihat [Jenis Casting](#type-casting).

## Boolean
`bool`: Nilai yang mungkin adalah konstanta `true` dan `false`.

Operator:

- `!` (negasi logis)
- `&&` (konjungsi logis, “dan”)
- `||` (disjungsi logis, “atau”)
- `==` (kesetaraan)
- `!=` (pertidaksamaan)

:::catatan
Operator `||` dan `&&` tidak menerapkan aturan hubung singkat yang umum. Ini berarti bahwa dalam ekspresi `f(x) || g(y)`, `g(y)` akan tetap dieksekusi meskipun `f(x)` bernilai true.
:::

## Bilangan bulat
`int`: Integer bertanda tangan dengan ukuran 64 bit.

Operator:

- Perbandingan: `<=`, `<`, `==`, `!=`, `>=`, `>` (semua evaluasi ke `bool`)
- Operator aritmatika: `+`, `-`, unary `-`, `*`, `/`, `%` (modulo).

Perhatikan kurangnya operator `**` (eksponensial) serta operator bitwise apa pun.

:::peringatan
Skrip akan gagal saat sisi kanan operasi Divisi dan modulo adalah nol.
:::

### Parsing Tanggal
Tanggal dan waktu selalu direpresentasikan sebagai bilangan bulat. Untuk mendapatkan stempel waktu UTC dari suatu tanggal, gunakan parser bawaan untuk menghindari potensi kesalahan. Ini akan mengambil tanggal dalam format `date("YYYY-MM-DDThh:mm:ss")` dan mengubahnya menjadi stempel waktu bilangan bulat.

#### Contoh

```solidity
int timestamp = date("2021-02-17T01:30:00");
require(timestamp == 1613554200);
```


## String
`string`: Urutan byte yang disandikan UTF8.

Operator:

- `+` (rangkaian)
- `==` (kesetaraan)
- `!=` (pertidaksamaan)

Anggota:

- `panjang`: Jumlah karakter dalam string.
- `split(int)`: Memisahkan string pada indeks yang ditentukan dan mengembalikan tuple dengan dua string yang dihasilkan.
- `reverse()`: Membalik string.

:::peringatan
Skrip akan gagal jika `split()` dipanggil dengan indeks yang di luar batas.
:::

## Byte
`bytes`: Urutan byte. Secara opsional dapat diikat ke panjang byte dengan menentukan mis. `bytes4`, `bytes32`, `bytes64`. Dimungkinkan juga untuk menggunakan `byte` sebagai alias untuk `bytes1`.

Operator:

- `+` (rangkaian)
- `==` (kesetaraan)
- `!=` (pertidaksamaan)

Anggota:

- `panjang`: Jumlah byte dalam urutan.
- `split(int)`: Membagi urutan byte pada indeks yang ditentukan dan mengembalikan tuple dengan dua urutan byte yang dihasilkan.
- `reverse()`: Membalik urutan byte.

:::peringatan
Skrip akan gagal jika `split()` dipanggil dengan indeks yang di luar batas.
:::

## Jenis byte dengan makna semantik
Beberapa urutan byte memiliki arti khusus di dalam kontrak Bitcoin Cash. Ini telah diberikan tipenya sendiri terpisah dari tipe `bytes` biasa.

### Kunci Publik
`pubkey`: Urutan byte yang mewakili kunci publik. Panjang umumnya 33 byte.

Operator:

- `==` (kesetaraan)
- `!=` (pertidaksamaan)

### Tanda Tangan Transaksi
`sig`: Urutan byte yang mewakili tanda tangan transaksi. Panjang umumnya 65 byte.

Operator:

- `==` (kesetaraan)
- `!=` (pertidaksamaan)

### Tanda Tangan Data
`datasig`: Urutan byte yang mewakili tanda tangan data. Panjang umumnya 64 byte.

Operator:

- `==` (kesetaraan)
- `!=` (pertidaksamaan)

## Himpunan
Array tidak dapat dialihkan dan hanya dapat digunakan dengan fungsi `checkMultisig` menggunakan sintaks berikut:

``` soliditas
checkMultisig([sig1, sig2], [pk1, pk2, pk3]);
```

##Tupel
Tuple adalah tipe yang dikembalikan saat memanggil fungsi anggota `split` pada tipe `string` atau `bytes`. Elemen pertama atau kedua mereka dapat diakses melalui sintaks pengindeksan yang mirip dengan bahasa lain:

``` soliditas
string pertanyaan = "Apa itu Bitcoin Cash?";
string jawaban = pertanyaan.split(15)[0].split(8)[1];
```

Dimungkinkan juga untuk menetapkan kedua sisi tuple sekaligus dengan sintaks yang merusak:

```solidity
string bitcoin, string cash = "BitcoinCash".split(7);
require(bitcoin == cash);
```

## Ketik Pengecoran
Pengecoran tipe dapat dilakukan baik secara eksplisit maupun implisit seperti yang diilustrasikan di bawah ini. `pubkey`, `sig` dan `datasig` dapat secara implisit ditransmisikan ke `bytes`, artinya mereka dapat digunakan di mana pun Anda biasanya menggunakan tipe `bytes`. Pengecoran tipe eksplisit dapat dilakukan dengan rentang tipe yang lebih luas, tetapi masih terbatas. Sintaks casting tipe eksplisit ini diilustrasikan di bawah ini. Perhatikan bahwa Anda juga dapat mentransmisikan ke tipe `bytes` yang dibatasi.

:::catatan
Saat mentransmisikan tipe bilangan bulat ke byte dengan ukuran tertentu, nilai bilangan bulat diisi dengan nol. misalnya `bytes4(0) == 0x00000000`. Dimungkinkan juga untuk menambahkan jumlah variabel nol, dengan meneruskan parameter `size`, yang menunjukkan ukuran keluaran. misalnya `byte(0, 4 - 2) == 0x0000`.
:::

:::peringatan
Saat mentransmisikan tipe byte ke integer, Anda harus yakin bahwa nilai byte cocok dengan integer bertanda 64-bit, atau skrip akan gagal.
:::

Lihat tabel berikut untuk informasi tentang tipe mana yang dapat dilemparkan ke tipe lain mana.

| Type    | Implicitly castable to | Explicitly castable to             |
| ------- | ---------------------- | ---------------------------------- |
| int     |                        | bytes, bool                        |
| bool    |                        | int                                |
| string  |                        | bytes                              |
| bytes   |                        | sig, pubkey, int                   |
| pubkey  | bytes                  | bytes                              |
| sig     | bytes                  | bytes                              |
| datasig | bytes                  | bytes                              |

#### Contoh
```solidity
pubkey pk = pubkey(0x0000);
bytes editedPk = bytes(pk) + 0x1234;
bytes4 integer = bytes4(25);
```
