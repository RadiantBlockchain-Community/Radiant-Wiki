---
title: Contoh
---

Kumpulan contoh yang lengkap tersedia di [repositori GitHub](https://github.com/Bitcoin-com/cashscript/tree/master/examples). Di bawah ini kami membahas beberapa contoh ini secara lebih rinci dan membahas fungsinya.

## Transfer Dengan Batas Waktu
Salah satu kasus penggunaan Bitcoin Cash yang menarik adalah menggunakannya untuk *paper tips*. Dengan paper tips, Anda mengirim sejumlah kecil uang ke suatu alamat, dan mencetak kunci pribadi yang sesuai di selembar kertas. Kemudian Anda dapat membagikan potongan kertas ini sebagai tip atau hadiah kepada orang-orang secara langsung. Namun dalam praktiknya, orang mungkin tidak tahu apa yang harus dilakukan dengan hadiah ini atau mereka mungkin kehilangan atau melupakannya.

Sebagai alternatif, smart contract dapat digunakan untuk jenis hadiah ini. Kontrak pintar ini memungkinkan penerima untuk mengklaim hadiahnya kapan saja, tetapi jika mereka tidak mengklaimnya tepat waktu, pengirim dapat mengklaimnya kembali.

```solidity
pragma cashscript ^0.7.0;

contract TransferWithTimeout(pubkey sender, pubkey recipient, int timeout) {
    // Require recipient's signature to match
    function transfer(sig recipientSig) {
        require(checkSig(recipientSig, recipient));
    }

    // Require timeout time to be reached and sender's signature to match
    function timeout(sig senderSig) {
        require(checkSig(senderSig, sender));
        require(tx.time >= timeout);
    }
}
```

## HodlVault
Baik atau buruk, HODLing dan menunggu kenaikan harga adalah salah satu hal utama yang ingin dilakukan orang dengan cryptocurrency mereka. Tetapi mungkin sulit untuk mempertahankan mata uang kripto Anda saat harganya turun. Jadi untuk mencegah tangan yang lemah mendapatkan yang terbaik dari Anda, lebih baik menyimpan simpanan Anda dalam kontrak pintar yang memberlakukan HODLing untuk Anda.

Kontrak pintar ini bekerja dengan menghubungkan dengan oracle harga. Peramal harga ini adalah entitas tepercaya yang menerbitkan harga BCH/USD setiap blok. Harga ini diteruskan ke dalam kontrak, dan hanya jika harganya lebih tinggi dari harga target Anda, Anda dapat membelanjakan koin.

Ini melibatkan tingkat kepercayaan tertentu pada oracle harga, tetapi karena oracle menghasilkan data harga untuk digunakan semua orang, insentif mereka untuk menyerang kontrak pintar *Anda* diminimalkan. Untuk memperbaiki situasi ini, Anda juga dapat memilih untuk terhubung dengan beberapa penyedia oracle sehingga Anda tidak perlu mempercayai satu pihak pun.

```solidity
pragma cashscript ^0.7.0;

// A minimum block is provided to ensure that oracle price entries from before
// this block are disregarded. i.e. when the BCH price was $1000 in the past,
// an oracle entry with the old block number and price can not be used.
contract HodlVault(pubkey ownerPk, pubkey oraclePk, int minBlock, int priceTarget) {
    function spend(sig ownerSig, datasig oracleSig, bytes oracleMessage) {
        // Decode the message { blockHeight, price }
        bytes4 blockHeightBin, bytes4 priceBin = oracleMessage.split(4);
        int blockHeight = int(blockHeightBin);
        int price = int(priceBin);


        // Check that blockHeight is after minBlock
        require(blockHeight >= minBlock);
        // Check that blockHeight is not in the future
        require(tx.time >= blockHeight);

        // Check that current price is at least priceTarget
        require(price >= priceTarget);

        // Check that the price message was signed by the oracle
        require(checkDataSig(oracleSig, oracleMessage, oraclePk));

        // Check that the transaction was signed by the contract owner
        require(checkSig(ownerSig, ownerPk));
    }
}
```

## Licho's Mecenas
Donasi adalah cara yang bagus untuk mendukung proyek yang Anda sukai dan donasi berkala dapat memberi insentif untuk peningkatan berkelanjutan pada produk. Tetapi platform seperti Patreon umumnya mengambil biaya 10%+ dan tidak menerima cryptocurrency. Sebagai gantinya, Anda dapat membuat kontrak pintar peer-to-peer yang memungkinkan penerima menarik jumlah tertentu setiap bulan.

Kontrak bekerja dengan memeriksa bahwa UTXO setidaknya berumur 30 hari, setelah itu menggunakan perjanjian untuk menegakkan bahwa jumlah `pledge` dikirim ke penerima, sedangkan sisanya dikirim kembali ke kontrak itu sendiri. Dengan mengirimkannya kembali, penghitung `tx.age` secara efektif diatur ulang, artinya proses ini hanya dapat diulang setelah 30 hari berlalu.

Karena sifat perjanjian, kami harus sangat spesifik tentang output (jumlah dan tujuan) transaksi. Ini juga berarti bahwa kami harus memperhitungkan kasus khusus di mana sisa saldo kontrak lebih rendah dari jumlah `pledge`, yang berarti tidak ada sisa yang harus dikembalikan. Akhirnya kami harus memperhitungkan sedikit biaya yang harus diambil dari saldo kontrak untuk membayar para penambang.

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
        // remainder to the recipient and the change back to the contract
        if (changeValue <= pledge + minerFee) {
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
