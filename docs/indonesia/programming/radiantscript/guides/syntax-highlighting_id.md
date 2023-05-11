---
title: Penyorotan Sintaks
---

Saat mengembangkan kontrak pintar untuk CashScript, ada gunanya memiliki penyorotan sintaks yang tepat di editor kode/IDE Anda. Jika Anda menggunakan Visual Studio Code, ada ekstensi CashScript khusus. Untuk editor lain, disarankan untuk menginstal plugin penyorotan Solidity dan mengaitkannya dengan file `.cash` di editor Anda, karena sintaks kedua bahasa tersebut sangat mirip.

## Visual Studio Code (Disarankan)
Untuk Visual Studio Code, [ekstensi CashScript](https://marketplace.visualstudio.com/items?itemName=nathanielcherian.cashscript) khusus dikembangkan oleh kontributor komunitas [Nathaniel Cherian](https://twitter.com/nathanielCheria). Plugin ini berfungsi dengan file `.cash` dan mendukung penyorotan sintaks, pelengkapan otomatis, cuplikan, linting, dan bahkan kompilasi terintegrasi.

Karena dukungan CashScript kelas satu, Visual Studio Code bersama dengan ekstensi CashScript ini adalah cara yang disarankan untuk mengembangkan kontrak CashScript.

## Teks Luhur
Plugin Solidity paling populer untuk Sublime Text 2/3 adalah [Ethereum](https://packagecontrol.io/packages/Ethereum). Instal plugin ini dengan [Package Control](https://packagecontrol.io/), buka file `.cash` dan atur Solidity sebagai bahasa sintaks di bilah menu Sublime:

> Lihat -> Sintaks -> Buka semua dengan ekstensi saat ini sebagai ... -> Soliditas

Ini mengaitkan file `.cash` dengan Solidity, dan mengaktifkan penyorotan sintaks untuk file CashScript Anda.

## Atom
Plugin Solidity paling populer untuk Atom adalah [language-solidity](https://atom.io/packages/language-solidity). Instal plugin ini dan tambahkan cuplikan berikut ini ke dalam file Config Anda:

```yaml title="&#126;/.atom/config.cson"
core:
  customFileTypes:
    "source.solidity": ["cash"]
```

Ini akan mengaitkan file `.cash` dengan Solidity, dan mengaktifkan penyorotan sintaks untuk file CashScript Anda.

## Vim
Plugin Solidity paling populer untuk Vim adalah [vim-solidity](https://github.com/TovarishFin/vim-solidity). Instal plugin ini dan tambahkan cuplikan berikut ke `.vimrc` Anda:

```bash title=".vimrc"
au BufRead,BufNewFile *.cash setfiletype solidity
```

Ini mengaitkan file `.cash` dengan Solidity, dan mengaktifkan penyorotan sintaks untuk file CashScript Anda.

## GitHub & GitLab
GitHub dan GitLab memiliki penyorotan sintaks untuk bawaan Solidity. Untuk mengaitkan file `.cash` dengan penyorotan Solidity, tambahkan file `.gitattributes` ke repositori Anda dengan konten berikut:

```python title=".gitattributes"
*.cash linguist-language=Solidity # GitHub
*.cash gitlab-language=solidity # GitLab
```

## Yang lain
Jika editor Anda tidak disebutkan di atas, langkah-langkahnya mungkin sangat mirip. Cobalah untuk menemukan plugin penyorotan sintaks Soliditas untuk editor pilihan Anda dan temukan metode untuk mengaitkan file `.cash` dengan penyorotan Soliditas ini.
