---
title: Menulis Perjanjian & Introspeksi
sidebar_label: Perjanjian & Introspeksi
---

Csemua perjanjian adalah keributan dalam kontrak pintar Bitcoin Cash. Tapi apa itu, dan bagaimana Anda menggunakannya? Dalam satu kalimat: **perjanjian adalah batasan tentang bagaimana uang dapat dibelanjakan**. Contoh sederhananya adalah membuat kontrak pintar yang **hanya** dapat mengirim uang ke satu alamat tertentu dan tidak ke tempat lain. Istilah *Kovenan* berasal dari hukum properti, yang digunakan untuk membatasi penggunaan objek apa pun - atau dalam kasus BCH, penggunaan uang.

Perjanjian Bitcoin pertama kali diusulkan dalam makalah berjudul [Bitcoin Covenants][bitcoin-covenants], tetapi beberapa proposal lain telah dibuat selama bertahun-tahun. Pada bulan Mei 2022, Bitcoin Cash menerapkan apa yang disebut *Introspeksi Asli* yang memungkinkan perjanjian yang efisien dan dapat diakses.

## Data introspeksi yang dapat diakses
Saat menggunakan CashScript, Anda dapat mengakses banyak *data introspeksi* yang dapat digunakan untuk memeriksa dan membatasi detail transaksi, seperti input dan output.

- **`int this.activeInputIndex`** - Index of the input that is currently under evaluation during transaction validation.
- **`bytes this.activeBytecode`** - Contract bytecode of the input that is currently under evaluation during transaction validation.
- **`int tx.version`** - Version of the transaction.
- **`int tx.locktime`** - `nLocktime` field of the transaction.
- **`int tx.inputs.length`** - Number of inputs in the transaction.
- **`int tx.inputs[i].value`** - Value of a specific input (in satoshis).
- **`bytes tx.inputs[i].lockingBytecode`** - Locking bytecode (`scriptPubKey`) of a specific input.
- **`bytes tx.inputs[i].unlockingBytecode`** - Unlocking bytecode (`scriptSig`) of a specific input.
- **`bytes32 tx.inputs[i].outpointTransactionHash`** - Outpoint transaction hash of a specific input.
- **`int tx.inputs[i].outpointIndex`** - Outpoint index of a specific input.
- **`int tx.inputs[i].sequenceNumber`** - `nSequence` number of a specific input.
- **`int tx.outputs.length`** - Number of outputs in the transaction.
- **`int tx.outputs[i].value`** - Value of a specific output (in satoshis).
- **`bytes tx.outputs[i].lockingBytecode`** - Locking bytecode (`scriptPubKey`) of a specific output.

## Menggunakan data introspeksi
Meskipun kami mengetahui bidang data individual, tidak segera jelas bagaimana ini dapat digunakan untuk membuat kontrak pintar yang berguna di Bitcoin Cash. Namun ada beberapa kendala yang bisa dibuat dengan menggunakan bidang-bidang tersebut, yang paling penting adalah kendala penerima dana, demikian yang akan kita bahas.

### Membatasi penerima P2PKH
Salah satu teknik menarik dalam Bitcoin Cash disebut blind escrow, artinya dana ditempatkan dalam kontrak escrow. Kontrak ini hanya dapat melepaskan dana ke salah satu peserta escrow, dan tidak memiliki kendali lain atas dana tersebut. Pertukaran lokal non-penahanan [LocalCryptos](https://localcryptos.com) menggunakan `OP_CHECKDATASIG` untuk melakukan ini, tetapi kami juga dapat mencapai hal serupa dengan membatasi penerima dengan perjanjian.## Menggunakan data introspeksi
Meskipun kami mengetahui bidang data individual, tidak segera jelas bagaimana ini dapat digunakan untuk membuat kontrak pintar yang berguna di Bitcoin Cash. Namun ada beberapa kendala yang bisa dibuat dengan menggunakan bidang-bidang tersebut, yang paling penting adalah kendala penerima dana, demikian yang akan kita bahas.

### Membatasi penerima P2PKH
Salah satu teknik menarik dalam Bitcoin Cash disebut blind escrow, artinya dana ditempatkan dalam kontrak escrow. Kontrak ini hanya dapat melepaskan dana ke salah satu peserta escrow, dan tidak memiliki kendali lain atas dana tersebut. Pertukaran lokal non-penahanan [LocalCryptos](https://localcryptos.com) menggunakan `OP_CHECKDATASIG` untuk melakukan ini, tetapi kami juga dapat mencapai hal serupa dengan membatasi penerima dengan perjanjian.

```solidity
contract Escrow(bytes20 arbiter, bytes20 buyer, bytes20 seller) {
    function spend(pubkey pk, sig s) {
        require(hash160(pk) == arbiter);
        require(checkSig(s, pk));

        // Check that the correct amount is sent
        int minerFee = 1000; // hardcoded fee
        int amount = tx.inputs[this.activeInputIndex].value - minerFee;
        require(tx.outputs[0].value == amount);

        // Check that the transaction sends to either the buyer or the seller
        bytes25 buyerLock = new LockingBytecodeP2PKH(buyer);
        bytes25 sellerLock = new LockingBytecodeP2PKH(seller);
        bool sendsToBuyer = tx.outputs[0].lockingBytecode == buyerLock;
        bool sendsToSeller = tx.outputs[0].lockingBytecode == sellerLock;
        require(sendsToBuyer || sendsToSeller);
    }
}
```

Kontrak dimulai dengan melakukan beberapa pemeriksaan untuk memastikan transaksi ditandatangani oleh arbiter. Selanjutnya memeriksa bahwa saldo kontrak penuh (`tx.inputs[this.activeInputIndex].value`) dikirim ke output pertama dengan mengakses `tx.outputs[0].value`. Terakhir ia memeriksa apakah penerima uang itu adalah pembeli atau penjual menggunakan `LockingBytecodeP2PKH` dan `tx.outputs[0].lockingBytecode`. Perhatikan bahwa kami menggunakan biaya hardcode karena sulit untuk menghitung biaya transaksi yang tepat di dalam kontrak pintar.

### Membatasi penerima P2SH
Selain mengirim uang ke alamat `P2PKH`, juga dimungkinkan untuk mengirim uang ke alamat smart contract (`P2SH`). Ini dapat digunakan dengan cara yang sama seperti alamat `P2PKH` jika hash skrip diketahui sebelumnya, tetapi ini juga dapat digunakan untuk memastikan bahwa uang harus dikirim kembali ke smart contract saat ini.

Ini sangat efektif bila digunakan bersamaan dengan kendala waktu. Contohnya adalah kontrak *Kehendak Terakhir Licho*. Kontrak ini menempatkan saklar orang mati pada kepemilikan kontrak, dan mengharuskan pemilik untuk mengirimkan detak jantung ke kontrak setiap enam bulan. Jika kontrak belum menerima detak jantung ini, pewaris dapat mengklaim dana sebagai gantinya.

```solidity
contract LastWill(bytes20 inheritor, bytes20 cold, bytes20 hot) {
    function inherit(pubkey pk, sig s) {
        require(tx.age >= 180 days);
        require(hash160(pk) == inheritor);
        require(checkSig(s, pk));
    }

    function cold(pubkey pk, sig s) {
        require(hash160(pk) == cold);
        require(checkSig(s, pk));
    }

    function refresh(pubkey pk, sig s) {
        require(hash160(pk) == hot);
        require(checkSig(s, pk));

        // Check that the correct amount is sent
        int minerFee = 1000; // hardcoded fee
        int amount = tx.inputs[this.activeInputIndex].value - minerFee;
        require(tx.outputs[0].value == amount);

        // Check that the funds are sent back to the contract
        bytes23 contractLock = tx.inputs[this.activeInputIndex].lockingBytecode;
        require(tx.outputs[0].lockingBytecode == contractLock);
    }
}
```

Kontrak ini memiliki tiga fungsi, tetapi hanya fungsi `refresh()` yang menggunakan perjanjian. Sekali lagi ia melakukan pemeriksaan yang diperlukan untuk memverifikasi bahwa transaksi ditandatangani oleh pemilik, setelah itu memeriksa bahwa seluruh saldo kontrak dikirim. Ini kemudian menggunakan `tx.inputs[this.activeInputIndex].lockingBytecode` untuk mengakses bytecode pengunciannya sendiri, yang dapat digunakan sebagai bytecode penguncian dari output ini. Mengirim kembali nilai penuh ke kontrak yang sama secara efektif akan mengatur ulang penghitung `tx.age`, sehingga pemilik kontrak perlu melakukan ini setiap 180 hari.

### Membatasi P2PKH dan P2SH
Contoh sebelumnya menunjukkan pengiriman uang hanya ke satu keluaran `P2PKH` atau `P2SH`. Namun tidak ada yang menghalangi kami untuk menulis kontrak yang dapat dikirim ke banyak keluaran, termasuk kombinasi keluaran `P2PKH` dan `P2SH`. Contoh yang baik adalah kontrak *Licho's Mecenas* yang memungkinkan Anda mengatur pembayaran berulang di mana penerima dapat mengklaim jumlah yang sama setiap bulan, sedangkan sisanya harus dikirim kembali ke kontrak.

```solidity
contract Mecenas(bytes20 recipient, bytes20 funder, int pledge, int period) {
    function receive() {
        require(tx.age >= period);

        // Check that the first output sends to the recipient
        bytes25 recipientLockingBytecode = new LockingBytecodeP2PKH(recipient);
        require(tx.outputs[0].lockingBytecode == recipientLockingBytecode);

        // Calculate the value that's left
        int minerFee = 1000;
        int currentValue = tx.inputs[this.activeInputIndex].value;
        int changeValue = currentValue - pledge - minerFee;

        // If there is not enough left for *another* pledge after this one,
        // we send the remainder to the recipient. Otherwise we send the
        // pledge to the recipient and the change back to the contract
        if (changeValue <= pledge + minerFees) {
            require(tx.outputs[0].value == currentValue - minerFee);
        } else {
            require(tx.outputs[0].value == pledge);
            bytes changeBytecode = tx.inputs[this.activeInputIndex].lockingBytecode;
            require(tx.outputs[1].lockingBytecode == changeBytecode);
            require(tx.outputs[1].value == changeValue);
        }
    }

    function reclaim(pubkey pk, sig s) {
        require(hash160(pk) == funder);
        require(checkSig(s, pk));
    }
}
```

Kontrak ini menerapkan teknik yang serupa dengan dua contoh sebelumnya untuk memverifikasi tanda tangan, meskipun dalam kasus ini tidak masalah siapa *penandatangan* transaksi tersebut. Karena output dibatasi dengan perjanjian, **tidak mungkin** seseorang dapat memanggil fungsi ini untuk mengirim uang **ke mana pun kecuali ke output yang benar**.

### Keadaan simulasi
Kasus penggunaan yang lebih lanjut untuk membatasi penerima disebut keadaan simulasi. Ini berfungsi dengan membatasi penerima ke **versi yang sedikit diubah dari kontrak saat ini**. Hal ini dapat dilakukan saat perubahan pada kontrak hanya pada parameter konstruktornya dan saat parameter ini memiliki ukuran yang diketahui (seperti `bytes20` atau `bytes4`).

Untuk mendemonstrasikan ini, kami mempertimbangkan kontrak Mecenas lagi, dan fokus pada kelemahan kontrak ini: Anda harus mengklaim dana pada saat yang tepat atau Anda meninggalkan uang di atas meja. Setiap kali Anda mengklaim uang dari kontrak, penghitung `tx.age` diatur ulang, sehingga klaim berikutnya dimungkinkan 30 hari setelah klaim sebelumnya. Jadi jika kita menunggu beberapa hari untuk mengklaim, **hari-hari ini pada dasarnya terbuang sia-sia**.

Selain hari-hari yang terbuang percuma ini, juga tidak nyaman untuk mengklaim pada interval yang ditentukan, daripada model "streaming" yang digunakan oleh proyek Ethereum [Sablier](https://www.sablier.finance/). Alih-alih menetapkan interval, Anda harus dapat mengklaim dana kapan saja selama "aliran uang". Dengan menggunakan keadaan simulasi, kita dapat mendekati sistem serupa dengan BCH.

```solidity
contract Mecenas(
    bytes20 recipient,
    bytes20 funder,
    int pledgePerBlock,
    bytes8 initialBlock,
) {
    function receive() {
        // Check that the first output sends to the recipient
        bytes25 recipientLockingBytecode = new LockingBytecodeP2PKH(recipient);
        require(tx.outputs[0].lockingBytecode == recipientLockingBytecode);

        // Check that time has passed and that time locks are enabled
        int initial = int(initialBlock);
        require(tx.time >= initial);

        // Calculate the amount that has accrued since last claim
        int passedBlocks = tx.locktime - initial;
        int pledge = passedBlocks * pledgePerBlock;

        // Calculate the leftover amount
        int minerFee = 1000;
        int currentValue = tx.inputs[this.activeInputIndex].value;
        int changeValue = currentValue - pledge - minerFee;

        // If there is not enough left for *another* pledge after this one,
        // we send the remainder to the recipient. Otherwise we send the
        // remainder to the recipient and the change back to the contract
        if (changeValue <= pledge + minerFee) {
            require(tx.outputs[0].value == currentValue - minerFee);
        } else {
            // Check that the outputs send the correct amounts
            require(tx.outputs[0].value == pledge);
            require(tx.outputs[1].value == changeValue);

            // Cut out old initialBlock (OP_PUSHBYTES_8 <initialBlock>)
            // Insert new initialBlock (OP_PUSHBYTES_8 <tx.locktime>)
            // Note that constructor parameters are added in reverse order,
            // so initialBlock is the first statement in the contract bytecode.
            bytes newContract = 0x08 + bytes8(tx.locktime) + this.activeBytecode.split(9)[1];

            // Create the locking bytecode for the new contract and check that
            // the change output sends to that contract
            bytes23 newContractLock = new LockingBytecodeP2SH(hash160(newContract));
            require(tx.outputs[1].lockingBytecode == newContractLock);
        }
    }

    function reclaim(pubkey pk, sig s) {
        require(hash160(pk) == funder);
        require(checkSig(s, pk));
    }
}
```

Alih-alih memiliki kontribusi per periode 30 hari, kami menetapkan kontribusi per blok. Setiap saat kita dapat menghitung berapa banyak uang yang diperoleh penerima. Maka perjanjian **memaksakan bahwa jumlah ini ditarik dari kontrak**. Sisanya dikirim ke aliran baru yang **dimulai di akhir aliran sebelumnya**. Bytecode aliran baru ini dihitung dengan "memotong" beberapa parameter konstruktor yang ada di kolom `this.activeBytecode` dan menggantinya dengan nilai baru. Proses ini dapat diterapkan ke aliran baru hingga uang di aliran habis.

Kelemahan menggunakan metode "status simulasi" ini adalah bahwa setiap aliran baru adalah kontrak baru dengan alamatnya sendiri. Jadi diperlukan abstraksi tambahan untuk menyediakan lapisan frontend yang jelas untuk sistem seperti ini. Keadaan yang disimulasikan dapat digunakan untuk membuat sistem yang jauh lebih canggih dan merupakan ide utama yang menggerakkan solusi kompleks seperti kartu pembayaran offline [Be.cash](https://be.cash), dan token Proof-of-Work SLP [MistCoin](https://mistcoin.org).

### Membatasi keluaran OP_RETURN
Cara terakhir untuk membatasi keluaran adalah menambahkan keluaran `OP_RETURN` ke dalam campuran. Hal ini diperlukan saat Anda ingin membuat perjanjian berbasis SLP, seperti [MistCoin](https://mistcoin.org) atau [SLP Mint Contracts](https://github.com/simpleledgerinc/slp-mint-kontrak). Integrasi SLP dan CashScript masih dalam tahap awal, jadi ini adalah topik untuk panduan lebih lanjut.

Saat ini kami akan menggunakan contoh yang lebih sederhana (tetapi juga kurang berguna), di mana kami membatasi smart contract hanya untuk dapat memposting di platform media sosial on-chain [Memo.cash](https://memo.cash) . Untuk ini kami menggunakan `LockingBytecodeNullData`, yang bekerja sedikit berbeda dari objek `LockingBytecode` lainnya. Sementara keluaran reguler memiliki skrip penguncian, keluaran `OP_RETURN` dapat menyertakan potongan data yang berbeda. Inilah mengapa `LockingBytecode` malah mengambil daftar potongan `bytes`.

```solidity
pragma cashscript ^0.7.0;

// This contract enforces making an announcement on Memo.cash and sending the
// remaining balance back to the contract.
contract Announcement() {
    function announce() {
        // Create the memo.cash announcement output
        bytes announcement = new LockingBytecodeNullData([
            0x6d02,
            bytes('A contract may not injure a human being or, '
             + 'through inaction, allow a human being to come to harm.')
        ]);

        // Check that the first tx output matches the announcement
        require(tx.outputs[0].value == 0);
        require(tx.outputs[0].lockingBytecode == announcement);

        // Calculate leftover money after fee (1000 sats)
        // Check that the second tx output sends the change back if there's
        // enough leftover for another announcement
        int minerFee = 1000;
        int changeAmount = tx.inputs[this.activeInputIndex].value - minerFee;
        if (changeAmount >= minerFee) {
            bytes changeLock = tx.inputs[this.activeInputIndex].lockingBytecode;
            require(tx.outputs[1].lockingBytecode == changeLock);
            require(tx.outputs[1].value == changeAmount);
        }
    }
}
```

Dalam kontrak ini kami membuat output "pengumuman" `OP_RETURN`, kami mencadangkan sebagian dari nilai untuk biaya penambang, dan akhirnya kami mengirimkan sisanya kembali ke kontrak.

## Kesimpulan
Kami telah membahas penggunaan utama perjanjian seperti yang ada di Bitcoin Cash saat ini. Kami telah melihat bagaimana kami dapat mencapai kasus penggunaan yang berbeda dengan menggabungkan pembatasan output transaksi ke output `P2SH` dan `P2PKH`. Kita juga menyentuh subjek yang lebih lanjut seperti keadaan simulasi dan keluaran `OP_RETURN`. Perjanjian adalah **faktor pembeda utama** untuk kontrak pintar BCH jika dibandingkan dengan BTC, sekaligus mempertahankan **verifikasi stateless efisien** yang sama. Jika Anda tertarik untuk mempelajari lebih lanjut tentang perbedaan kontrak pintar antara BCH, BTC, dan ETH, baca artikel [*Kontrak pintar di Ethereum, Bitcoin, dan Bitcoin Cash*](https://kalis.me/smart-contracts-eth-btc-bch/).

[bitcoin-covenants]: https://fc16.ifca.ai/bitcoin/papers/MES16.pdf
[bip68]: https://github.com/bitcoin/bips/blob/master/bip-0068.mediawiki
