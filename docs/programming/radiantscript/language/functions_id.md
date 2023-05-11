---
title: Fungsi Global & Operator
---

CashScript memiliki beberapa fungsi bawaan untuk hal-hal seperti aplikasi kriptografi dan aritmatika. Ini juga mencakup banyak operator umum, meskipun beberapa yang penting hilang karena keterbatasan Script Bitcoin yang mendasarinya.

## Fungsi aritmatika
### abs()
```solidity
int abs(int a)
```

Mengembalikan nilai absolut dari argumen `a`.

### min()
```solidity
int min(int a, int b)
```

Mengembalikan nilai minimum argumen `a` dan `b`.

### max()
```solidity
int max(int a, int b)
```

Mengembalikan nilai maksimum argumen `a` dan `b`.

### within()
```solidity
bool within(int x, int lower, int upper)
```

Mengembalikan `true` dan hanya jika `x >= lower && x < upper`.

## Fungsi hashing
### ripemd160()
```solidity
bytes20 ripemd160(any x)
```

Mengembalikan hash argumen RIPEMD-160 `x`.

### sha1()
```solidity
bytes20 sha1(any x)
```

Mengembalikan hash argumen SHA-1 `x`.

### sha256()
```solidity
bytes32 sha256(any x)
```

Mengembalikan hash argumen SHA-256 `x`.

### hash160()
```solidity
bytes20 hash160(any x)
```

Mengembalikan hash RIPEMD-160 dari hash argumen SHA-256 `x`.

### hash256()
```solidity
bytes32 hash256(any x)
```

Mengembalikan hash argumen SHA-256 ganda `x`.

## Fungsi pemeriksaan tanda tangan
:::peringatan
Semua fungsi pemeriksaan tanda tangan harus mematuhi aturan [NULLFAIL][bip146]. Ini berarti bahwa jika Anda ingin menggunakan hasil pemeriksaan tanda tangan di dalam kondisi pernyataan if, tanda tangan masukan harus benar, atau larik byte kosong. Saat Anda menggunakan tanda tangan yang salah sebagai masukan, skrip akan gagal.
:::

### checkSig()
```solidity
bool checksig(sig s, pubkey pk)
```

Periksa apakah tanda tangan transaksi `s` valid untuk transaksi saat ini dan cocok dengan kunci publik `pk`.

### checkMultiSig()
```solidity
bool checkMultiSig(sig[] sigs, pubkey[] pks)
```

Melakukan pemeriksaan multi-tanda tangan menggunakan daftar tanda tangan transaksi dan kunci publik.

:::catatan
Meskipun fungsi ini dapat digunakan di dalam kontrak pintar Anda, ini tidak didukung oleh SDK JavaScript, jadi disarankan untuk tidak menggunakannya. Alih-alih panggilan `checkMultiSig()` dapat disimulasikan menggunakan beberapa panggilan `checkSig()`.
:::

### checkDataSig()
```solidity
bool checkDataSig(datasig s, bytes msg, pubkey pk)
```

Checks bahwa sig `s` adalah tanda tangan yang valid untuk pesan `msg` dan cocok dengan kunci publik `pk`.

## Operator
Ikhtisar semua operator yang didukung dan prioritasnya disertakan di bawah ini. Yang perlu diperhatikan adalah kurangnya eksponensial, karena operasi ini tidak didukung oleh Script Bitcoin yang mendasarinya.

| Precedence | Description                         | Operator                 |
| ---------- | ----------------------------------- | ------------------------ |
| 1          | Parentheses                         | `(<expression>)`         |
| 2          | Type cast                           | `<type>(<expression>)`   |
| 3          | Object instantiation                | `new <class>(<args...>)` |
| 4          | Function call                       | `<function>(<args...>)`  |
| 5          | Tuple index                         | `<tuple>[<index>]`       |
| 6          | Member access                       | `<object>.<member>`      |
| 7          | Unary minus                         | `-`                      |
| 7          | Logical NOT                         | `!`                      |
| 8          | Multiplication, division and modulo | `*`, `/`, `%`            |
| 9          | Addition and subtraction            | `+`, `-`                 |
| 9          | String / bytes concatenation        | `+`                      |
| 10         | Numeric comparison                  | `<`, `>`, `<=`, `>=`     |
| 11         | Equality and inequality             | `==`, `!=`               |
| 12         | Bitwise AND                         | `&`                      |
| 13         | Bitwise XOR                         | `^`                      |
| 14         | Bitwise OR                          | \|                       |
| 15         | Logical AND                         | `&&`                     |
| 16         | Logical OR                          | \|\|                     |
| 17         | Assignment                          | `=`                      |

[bip146]: https://github.com/bitcoin/bips/blob/master/bip-0146.mediawiki
