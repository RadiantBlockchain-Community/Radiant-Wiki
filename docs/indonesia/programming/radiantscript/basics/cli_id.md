---
title: Command Line Interface
---

TAntarmuka baris perintah `cashc` digunakan untuk mengkompilasi file `.cash` CashScript menjadi file artefak `.json`. Artefak ini dapat diimpor dan digunakan oleh JavaScript SDK atau library/aplikasi lain yang menggunakan CashScript. Untuk informasi selengkapnya tentang format artefak ini, lihat [Artefak](/dokumen/bahasa/artefak).

## Instalasi
Anda dapat menggunakan `npm` untuk menginstal alat baris perintah `cashc` secara global.

```bash
npm install -g cashc
```

## Usage
Alat CLI `cashc` dapat digunakan untuk mengkompilasi file `.cash` ke file artefak JSON.

```bash
Usage: cashc [options] [source_file]

Options:
  -V, --version Keluarkan nomor versi.
  -o, --output <path> Tentukan file untuk mengeluarkan artefak yang dihasilkan.
  -h, --hex Kompilasi kontrak ke format hex daripada artefak lengkap.
  -A, --asm Kompilasi kontrak ke format ASM daripada artefak lengkap.
  -c, --opcount Menampilkan jumlah opcode dalam bytecode yang dikompilasi.
  -s, --size Menampilkan ukuran dalam byte dari bytecode yang dikompilasi.
  -?, --help Menampilkan bantuan
```
