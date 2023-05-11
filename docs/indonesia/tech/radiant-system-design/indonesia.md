---
hide:
  - toc
---
# Desain Sistem Blockchain Radiant

Pengembang Radiant

11 Agustus, 2022

radiantblockchain.org

**Abstrak**

Jaringan Radiant adalah sistem aset digital peer-to-peer yang memungkinkan pertukaran nilai secara langsung tanpa melalui pihak pusat. Protokol asli Bitcoin[1] menyediakan apa yang diperlukan untuk membuat sistem kas elektronik peer-to-peer, tetapi tidak memiliki kemampuan untuk memverifikasi riwayat transaksi dan karenanya tidak dapat digunakan untuk memvalidasi aset digital. Tanda tangan digital dan batasan keluaran memberikan bagian dari solusi, tetapi manfaat utama hilang jika pihak ketiga yang tepercaya masih diperlukan untuk memvalidasi aset digital. Jaringan Radiant itu sendiri membutuhkan struktur minimal, dan beroperasi serupa dengan jaringan Bitcoin dalam melakukan timestamping transaksi ke dalam rantai bukti kerja berbasis hash yang sedang berlangsung. Kami memperkenalkan dua teknik untuk memvalidasi aset digital menggunakan sistem bukti induksi tujuan umum yang beroperasi dalam ruang dan waktu O(1) konstan. Sistem bukti induksi memungkinkan untuk menyusun keluaran secara efisien dengan cara apa pun, tanpa mengorbankan karakteristik paralelisme dan skalabilitas yang melekat pada arsitektur berbasis UTXO. Pengguna dapat keluar dan bergabung kembali dengan jaringan sesuka hati dan yakin akan integritas dan keaslian aset digital mereka.

# Perkenalan  

Perdagangan dengan blockchain dan buku besar digital bergantung pada penerbit dan kustodian yang berfungsi sebagai pihak ketiga tepercaya (terkadang disebut sebagai "jembatan", "ramalan", "lapisan sekunder") untuk mengautentikasi aset digital dan memproses pembayaran elektronik. Meskipun sistem bekerja cukup baik untuk transaksi seperti pembayaran elektronik, sistem ini masih memiliki kelemahan yang melekat pada model berbasis kepercayaan untuk penggunaan blockchain yang lebih maju. Tingginya biaya transaksi yang terkait dengan blockchain berbasis Ethereum Virtual Machines (EVM) disebabkan oleh ruang blok yang terbatas dan keterbatasan yang melekat pada model pemrosesan berbasis akun.

Yang dibutuhkan adalah sistem pembayaran elektronik yang juga dapat bertindak sebagai sistem manajemen aset digital dengan karakteristik kinerja dari arsitektur blockchain unspent transaction output (UTXO), dengan fleksibilitas blockchain berbasis akun. Dalam makalah ini, kami mengusulkan solusi untuk masalah penskalaan blockchain menggunakan dua metode baru yang, secara independen, menyediakan sistem bukti induksi umum yang mampu mengautentikasi aset digital, meniru blockchain berbasis akun, sambil mempertahankan karakteristik kinerja blockchain berbasis UTXO seperti itu. sebagai skala tak terbatas dan paralelisme.

Protokol asli Bitcoin[1] menyediakan apa yang diperlukan untuk membuat sistem aset digital peer-to-peer, tetapi tidak memiliki kemampuan untuk memverifikasi riwayat transaksi dan akibatnya tidak dapat mengautentikasi aset digital. Blockchain seperti Bitcoin Cash (BCH) dan Bitcoin Satoshi Vision (BSV) mencoba mengautentikasi aset digital melalui pihak ketiga tepercaya yang disebut "oracles" yang mengindeks transaksi yang relevan. Solusi semacam itu, bagaimanapun, mencegah kemungkinan kontrak blockchain lanjutan karena diperlukan kustodian tepercaya. Untuk mengatasi masalah keaslian aset digital, tanpa menggunakan pihak pusat, kami memperkenalkan dua metode baru yang beroperasi dalam ruang dan waktu O(1) yang konstan. Instruksi pemrograman tambahan menciptakan sistem bukti induksi tujuan umum. Pengguna dan aplikasi hanya perlu memverifikasi bahwa transfer aset digital terbaru diterima ke dalam blok.

Radiant adalah blockchain output transaksi yang tidak terpakai (UTXO) pertama yang memecahkan masalah utama yang mencegah pengembangan kontrak lanjutan pada blockchain lain seperti Bitcoin, Cardano, dan Dash. Desain terobosan ini merevolusi apa yang kami bayangkan dapat dilakukan dengan blockchain; Radiant adalah Turing Complete lapisan kinerja tinggi satu blockchain tanpa perlu lapisan sekunder.

![Positioning of various blockchains.](images/w1.jpeg)
<center>Pemosisian Radiant relatif terhadap blockchain populer.</center>
<br/>

# Masalah

Ada tiga masalah yang membuatnya tidak praktis untuk menggunakan blockchain keluaran transaksi yang tidak terpakai (UTXO) sebagai buku besar digital tujuan umum. Masalah pertama adalah kemampuan untuk secara sewenang-wenang membatasi kondisi pembelanjaan &mdash; atau kondisi maju pada semua transaksi keturunan. Masalah kedua adalah bagaimana mengotentikasi hasil transaksi secara efisien untuk memastikannya berasal dari transaksi asal yang valid &mdash; ini adalah persyaratan penting untuk banyak program, terutama untuk meniru akun dan membuat token yang sepadan. Masalah ketiga adalah koordinasi dan kolaborasi antar kontrak &mdash; kontrol yang tepat dari pengiriman pesan antara output transaksi. Kami akan menunjukkan bahwa ketiga masalah tersebut dapat diselesaikan tanpa mengorbankan kinerja atau skalabilitas model blockchain berbasis UTXO.

## Kendala Kontrak

Hambatan pertama untuk memprogram dengan blockchain unspent transaction output (UTXO) adalah kesalahpahaman tentang desain asli Satoshi Nakamoto dan kode pemrograman yang tersedia dalam protokol Bitcoin asli. Tidak diakui secara umum tetapi blockchain Bitcoin asli memiliki semua kode pemrograman yang diperlukan untuk kontrak pintar Turing Complete [2]. Kode pemrograman yang diperlukan telah dihapus dari protokol dalam pemutakhiran BTC tahun 2015.

Metode untuk memaksakan batasan pada kondisi pengeluaran adalah mengembalikan semua kode pemrograman asli dari Bitcoin dan menyediakan metode untuk memeriksa konteks transaksi saat ini. Ada dua cara untuk memeriksa transaksi saat ini sebagai jenis introspeksi. Cara pertama adalah dengan mendorong Signature Hash (dikenal sebagai "SigHash Preimage") ke stack dan menggunakan kunci pribadi sementara untuk menghasilkan tanda tangan dan kemudian menerapkan operasi `OP_CHECKSIG` untuk memvalidasi SigHash Preimage yang diharapkan untuk transaksi saat ini adalah benar. Cara kedua adalah menyediakan kode pemrograman introspeksi asli yang mendorong komponen transaksi yang relevan ke tumpukan untuk digunakan dalam skrip pembuka kunci.

Perbedaan utama dengan blockchain UTXO adalah tidak ada loop dalam kode pemrograman. Namun dalam praktiknya setiap pengulangan dapat disimulasikan dengan membuka gulungan operasi loop dan mereplikasi logika untuk jumlah pengulangan maksimum yang diperlukan. Dengan cara ini, blockchain UTXO dapat menghindari konsep "biaya waktu eksekusi" dan sebagai gantinya memperkirakan biaya eksekusi hanya dengan menggunakan ukuran skrip transaksi. Untuk alasan ini, disarankan agar blockchain UTXO memiliki ukuran transaksi maksimum yang cukup besar, seperti 2 megabita atau lebih untuk dapat mengakomodasi setiap kasus penggunaan yang mungkin memerlukan puluhan atau ratusan iterasi loop.

##  Kontrak Identitas Persisten

Koin elektronik didefinisikan sebagai rantai tanda tangan digital. Koin dimulai pada transaksi awal yang disebut transaksi "coinbase". Untuk mentransfer koin, pemilik keluaran yang tidak terpakai menandatangani koin dengan kunci pribadi mereka dan mengunci token dalam keluaran baru yang dikaitkan dengan kunci publik penerima. Pada setiap transaksi pengidentifikasi transaksi baru dan indeks keluaran digunakan, yang unik secara global. Konsep "saldo dompet" untuk pengguna adalah jumlah total dari unit token nominal yang dikontrol oleh pengguna untuk keluaran yang tidak terpakai untuk kunci publik yang sesuai. Setiap koin pada dasarnya diidentifikasi secara unik oleh keluaran terbaru yang tidak terpakai. Tidak ada konsep yang melekat pada "akun" atau "identitas koin".

Dalam blockchain keluaran transaksi yang tidak terpakai (UTXO), unit token asli adalah satu-satunya kelas &mdash; atau jenis dan oleh karena itu identitas koin persisten yang unik tidak diperlukan. Cukup memiliki identitas UTXO yang berbeda untuk menghitung koin yang dapat dibelanjakan. Namun, jika kita ingin membuat kelas token yang berbeda, dengan kata lain untuk "mewarnai" token asli untuk mewakili pembagian, poin, atau jenis enumerable lainnya, maka kita memerlukan cara untuk merepresentasikan dan memvalidasi keanggotaan kelas token secara efisien. Istilah "koin berwarna" telah digunakan untuk mendeskripsikan jaringan overlay yang mencetak token dari kejadian khusus atau transaksi pencetakan &mdash; mirip dengan koin asli yang dipancarkan dari basis koin.

Koin digital khusus (atau berwarna) didefinisikan sebagai rantai tanda tangan digital yang ditambatkan pada keluaran asal yang ditentukan pengguna. Pengguna dapat mencetak atau membuat penerbitan koin khusus dengan menyetor jumlah unit token asli yang diinginkan pada output dan menetapkannya sebagai basis koin dengan 36 0x00 null byte sebagai data push pertama dari output. Logika kontrak dibatasi sedemikian rupa sehingga pengeluaran keluaran berikutnya harus menyematkan titik keluar (id transaksi, indeks keluaran) dari transaksi asal ke dalam data push pertama (di mana 36 0x00 null byte berada dalam transaksi asal) untuk seluruh siklus hidup dari koin berwarna. Mengikuti konvensi ini dikombinasikan dengan batasan kontrak, kita dapat melihat bahwa teknik ini secara efektif "mewarnai" token asli dan dapat diidentifikasi dengan jelas. Logika tambahan dapat ditambahkan sesuai dengan kebutuhan aplikasi seperti bagaimana koin ditebus atau dikembalikan ke unit token aslinya. Misalnya penerbit dapat melakukan operasi atau pemegang token dapat "mencairkan" unit token asli dan secara efektif menghancurkan klasifikasi warna.
Teknik penyematan keluaran genesis membentuk pengidentifikasi unik global yang terkadang disebut sebagai pengidentifikasi aset (atau disingkat assetId) atau pengidentifikasi kontrak (atau disingkat contractId) yang sekarang dapat digunakan untuk mengidentifikasi koin yang termasuk dalam kelas koin tersebut. Identitas ini akan menjadi dasar penggunaan lanjutan yang diuraikan di bawah ini.

![Replay attack](images/a1.jpeg)
<center>Diagram 1. Serangan replay token </center>
<br/>

Namun ada masalah yang luar biasa: Bagaimana transaksi pembelanjaan dapat memastikan bahwa hanya koin yang berasal dari transaksi asal yang sah yang dapat dibelanjakan dan tidak diteruskan untuk membelanjakan pemalsuan yang hanya disalin?

## Ketertelusuran Kontrak &amp; Keaslian

Ingatlah bahwa keluaran transaksi yang tidak terpakai (UTXO) tidak memiliki identitas tetap, tetapi kami dapat memberikan identitas tetap dengan mengikuti aturan bahwa pengguna dapat menetapkan beberapa transaksi sebagai kejadian pembuatan awal, di mana titik keluar berdiri sebagai assetId atau contractId. Namun, menggunakan konvensi ini saja tidak cukup karena penyerang dapat menyalin salah satu pembelanjaan transaksi perantara dan memulai rangkaian tanda tangan baru (albiet forgery) untuk memalsukan kelas koin dan menyebarkannya. Setiap transaksi pembelanjaan tidak dapat membedakan antara keluaran nyata yang berasal dari keturunan yang valid versus pemalsuan dari salinan palsu. Yang diperlukan adalah cara untuk menegakkan keunikan global yang tidak mencolok dan efisien untuk diverifikasi di dalam skrip pengeluaran.

![Man in the middle](images/a2.jpeg)
<center>Diagram 2. Serangan pemalsuan token man-in-the-middle </center>
<br/>

### OP_PUSHINPUTREF <outpoint>: Referensi push

Kami mendefinisikan kode operasi pemrograman (kode OP) `OP_PUSHINPUTREF <hash>` didefinisikan sebagai valid sesuai:

1. Sebuah `OP_PUSHINPUTREF` mungkin hanya muncul dalam output dan membutuhkan tepat 36 byte segera setelah itu diperlakukan sebagai push ke stack dalam konteks interpretor.

2. Transaksi yang berisi output dengan `OP_PUSHINPUTREF` valid jika dan hanya jika argumen yang diberikan sama dengan salah satu outpoint input yang digunakan atau setidaknya salah satu bytecode skrip penguncian output input juga berisi `OP_PUSHINPUTREF yang sama `nilai argumen.

Satu-satunya cara `OP_PUSHINPUTREF` dapat muncul pertama kali dalam output adalah jika kejadian pertama sama dengan salah satu outpoint input yang digunakan. Dalam kasus penggunaan "Identitas Kontrak Persisten" di atas, ini sesuai dengan transaksi yang berisi 36 0x00 null byte yang menandakan asal pembuatan koin untuk kelas koin kustom (berwarna).

![OP_PUSHINPUTREF: Transaction References Minting TX](images/a3.jpeg)
<center>Diagram 3. Mencetak referensi transaksi OP_PUSHINPUTREF harus sesuai dengan outpoint.</center>
<br/>

![OP_PUSHINPUTREF: Transfer transaction OP_PUSHINPUTREF reference must match one of the previous outputs scripts being spent.](images/a4.jpeg)
<center>Diagram 4. Transaksi transfer Referensi OP_PUSHINPUTREF harus cocok dengan salah satu skrip keluaran sebelumnya yang digunakan.</center>
<br/>

![OP_PUSHINPUTREF: Invalid TX with no matching previous output script or outpoint](images/a5.jpeg)
<center>Diagram 5. TX tidak valid tanpa skrip keluaran atau outpoint sebelumnya yang cocok.</center>
<br/>

Kami menunjukkan bahwa aturan sederhana ini cukup untuk membentuk pengidentifikasi unik global, dan tidak membawa overhead &mdash; karena tidak diperlukan indeks tambahan atau tabel pencarian. Hanya transaksi dan input induk langsungnya yang diperlukan untuk memvalidasi keaslian &mdash; semua data tersedia untuk mesin virtual pada saat evaluasi skrip unlocking dan juga untuk menerima transaksi ke dalam mempool dan selanjutnya ke dalam blok.

Selama setidaknya salah satu koin masukan memiliki hash 36 byte yang valid &mdash; baik sebagai outpoint itu sendiri (artinya rantai asal pertama dari koin berwarna) atau sebagai salah satu skrip yang berisi referensi, maka identitas itu ada sebagai identitas yang bertahan. Untuk menghentikan silsilah, cukup abaikan penerusan referensi dan itu menghentikan kemampuan untuk menggunakan pengidentifikasi unik itu di UTXO lainnya selamanya.

Meskipun kode OP tunggal ini sudah cukup, ada beberapa kode OP tambahan yang memberikan fleksibilitas bagi pemrogram dan dijelaskan selanjutnya yang melengkapi `OP_PUSHINPUTREF`.

### OP_REQUIREINPUTREF <outpoint>: Membutuhkan referensi

Fungsi `OP_REQUIREINPUTREF` identik dengan `OP_PUSHINPUTREF` kecuali ia tidak meneruskan identitas referensi ke keluaran di mana ia muncul. Ini berguna untuk meminta setidaknya satu input dari kelas koin tertentu &mdash; tetapi tanpa segera mewariskan referensi.

![OP_REQUIREINPUTREF: TX](images/a6.jpeg)
<center>Diagram 6. Fungsi OP_REQUIREINPUTREF sama dengan OP_PUSHINPUTREF tetapi tanpa mendorong referensi ke output saat ini.</center>
<br/>

### OP_DISALLOWPUSHINPUTREF <outpoint>: Pelarangan referensi dalam Output

Untuk melarang penggunaan `OP_PUSHINPUTREF` dalam output, `OP_DISALLOWPUSHINPUTREF` dapat digunakan. Ini adalah kode OP yang berguna untuk smart contract yang membiarkan output terbuka untuk digunakan dalam berbagai konteks, tetapi memungkinkan pembuat kontrak untuk membatasi penerusan referensi, misalnya dalam output perubahan kustom.

![OP_DISALLOWPUSHINPUTREF: Disallow usage of a specific reference](images/a7.jpeg)
<center>Diagram 7. OP_DISALLOWPUSHINPUTREF melarang penggunaan referensi tertentu. </center>
<br/>

### OP_DISALLOWPUSHINPUTREFSIBLING <outpoint>: Pelarangan referensi dalam sibling outputs

Mirip dengan `OP_DISALLOWPUSHINPUTREF`, larang output tertentu di output saudara mana pun untuk referensi spesifik. Ini secara efektif melarang penggunaan referensi di lebih dari satu keluaran dan merupakan cara untuk membuat titik keluar tunggal. Dengan menggunakan `OP_DISALLOWPUSHINPUTREFSIBLING` dalam output, kita dapat membuat kontrak Non-Fungible Token (NFT) yang sederhana dan kuat yang berfungsi dengan bendera tanda tangan `SIGHASH_SINGLE`.

![OP_DISALLOWPUSHINPUTREFSIBLING: Disallow usage of a specific reference in all other outputs than the one in which this instruction appears](images/a8.jpeg)
<center>Diagram 8. OP_DISALLOWPUSHINPUTREFSIBLING: Larang penggunaan referensi tertentu di semua keluaran selain yang menampilkan instruksi ini</center>
<br/>

### OP_REFHASHDATASUMMARY_UTXO: Dorong ringkasan data UTXO

Memberikan ringkasan isi output yang dibelanjakan dalam transaksi saat ini. Mengambil elemen teratas tumpukan yang merupakan indeks dari input yang dihabiskan dan kemudian mendorong hash256 dari informasi tentang UTXO yang dihabiskan: `hash256(<nValue><hash256(scriptPubKey)><numRefs><hash256(sorted_list( pushInputRefs))>)`. Selama membuka evaluasi skrip, data yang relevan dari UTXO dapat diakses dan dimasukkan ke dalam logika. 

![OP_REFHASHDATASUMMARY_UTXO: OP_REFHASHDATASUMMARY_UTXO: Output coloring diagram](images/a9.jpeg)
<center>Diagram 9. OP_REFHASHDATASUMMARY_UTXO: Diagram pewarnaan keluaran </center>
<br/>

![OP_REFHASHDATASUMMARY_UTXO: Push to stack the summary of an input being spent.](images/a10.jpeg)
<center>Diagram 10. OP_REFHASHDATASUMMARY_UTXO: Dorong untuk menumpuk ringkasan input yang dihabiskan. </center>
<br/>

### OP_REFHASHVALUESUM_UTXOS: Dorong jumlah nilai UTXO dengan referensi (warna)

Kode pemrograman ini menerima hash256 dari referensi 36 byte dan mendorong ke stack jumlah total dari semua input yang cocok dengan pewarnaan referensi tersebut. Ini berguna untuk menyimpan data dan dengan cepat menilai input total dan nilai input ke transaksi.

Ini sangat berguna untuk membangun sistem akunting token yang sepadan seperti yang akan kita lihat di bawah.

![OP_REFHASHVALUESUM_UTXOS: Push to stack the total sum of the inputs which match a specific reference hash](images/a11.jpeg)
<center>Diagram 11. OP_REFHASHVALUESUM_UTXOS: Dorong untuk menumpuk jumlah total input yang cocok dengan hash referensi tertentu</center>
<br/> 

## Keaslian Kontrak melalui Induksi 

Metode lain untuk memecahkan masalah ketertelusuran dan keaslian adalah dengan mengizinkan penyematan transaksi induk ke dalam skrip pembuka kunci. Dengan cara ini, kami dapat melakukan bukti induksi dan menjamin bahwa keluaran transaksi berasal dari peristiwa pencetakan genesis yang valid.

Prinsip umum dalam induksi matematika adalah membuktikan bahwa beberapa pernyataan P(k) berlaku untuk k = 0, k = 1, k = 2... dan seterusnya sehingga P(k) berlaku untuk P(0) dan P( k + 1).

Dalam kasus kontrak pintar, kami ingin membuktikan bahwa transaksi yang diberikan valid dalam kasus dasar, seperti di dalamnya turun dari induk yang valid, kasus dasar P(0), dan dalam kasus induktif bahwa induk induk juga memenuhi kondisi, yang merupakan langkah P(k + 1).

Dengan bukti induksi, tidak mungkin memalsukan transaksi perantara karena transaksi grand parent tidak akan menjadi originasi yang disyaratkan.

![Diagram 12. Verify the parent and grand parent to identify forgery.](images/a12.jpeg)
<center>Diagram 12. Verifikasi orang tua dan kakek nenek untuk mengidentifikasi pemalsuan.</center>

<br/>
Namun sistem ini tidak praktis karena setiap kali keluaran dihabiskan, salinan lengkap dari transaksi induk (dan induknya) harus disematkan untuk menghitung pengenal transaksi. Hal ini menyebabkan ledakan faktorial, atau eksponensial, dalam ukuran transaksi. Ini tidak praktis karena hanya setelah menghabiskan sekitar selusin, ukuran transaksi mulai melebihi 1 GB dan terus bertambah secara eksponensial.
<br/>

![Diagram 13. Transaction Contents with Embedded Parents](images/a13.jpeg)
<center>Diagram 13. Konten Transaksi dengan Induk Tertanam</center>
<br/>
Untuk mengatasi masalah pertumbuhan ukuran transaksi eksponensial ini, kami memanfaatkan kolom "nVersion" yang disediakan oleh format transaksi. Bitcoin sudah memiliki transaksi versi 1 dan versi 2 dan kami hanya membuat versi 3 yang menggunakan algoritme pembuatan pengidentifikasi transaksi yang berbeda alih-alih mencirikan seluruh byte transaksi. Format transaksi versi 3 identik kecuali id transaksi dihasilkan dari struktur data ukuran tetap perantara yang memampatkan konten transaksi menjadi preimage &mdash; yang dapat disematkan dalam skrip pengunci untuk mendapatkan id transaksi dan menghindari masalah ukuran transaksi eksponensial.

### Pengidentifikasi Transaksi Versi 3

Mirip dengan algoritma Signature Hash yang menghasilkan "Sighash Preimage", kami menghasilkan preimage TxId sesuai dengan komponen dan bidang transaksi berikut.

> 1. nVersi transaksi (4 byte little endian)
> 2. nTotalInputs (4 byte little endian)
> 3. hashPrevoutInputs (hash 32 byte)
> 4. hashSequence (hash 32 byte)
> 5. nTotalOutputs (4 byte little endian)
> 6. hashOutputHash (hash 32 byte)
> 7. nLocktime transaksi (4 byte little endian)

<center>Diagram 14. Pengidentifikasi Transaksi Preimage Versi 3 </center>
<br/>

Dengan menambah bidang nVersion, kami memperkenalkan cara untuk mengompres seluruh transaksi ke dalam ukuran tetap (128 byte) yang dapat didorong ke tumpukan, dan di-hash untuk mencapai pengidentifikasi Transaksi, dan karenanya menyelesaikan masalah peningkatan ukuran eksponensial dari dari transaksi induk yang disematkan dalam bukti induksi.

![Transaction Id v3 Preimage Embedded](images/a15.jpeg)
<center>Diagram 15. Transaction Identifier Version 3 Preimages with Embedded Parents</center>
<br/>

Perhatikan bahwa sistem ini sendiri cukup untuk membuat bukti induksi arbitrer dan untuk tujuan umum. Ini adalah metode kedua di mana bukti induksi arbitrer dapat dibuat selain teknik `OP_PUSHINPUTREF` yang telah dibahas.
 
## Peningkatan Algoritma Signature Hash 

Dibangun di atas Preimage Id Transaksi teknik segmentasi keluaran, kita dapat memutakhirkan algoritme Sighash default dengan bidang tambahan yang disebut `hashOutputsHashes` untuk membuatnya lebih mudah membatasi keluaran dan menghemat ruang dan logika.

> 1. nVersi transaksi (4 byte little endian)
> 2. hashPrevouts (hash 32 byte)
> 3. hashSequence (hash 32 byte)
> 4. outpoint (hash 32 byte + 4 byte little endian)
> 5. scriptCode input (serial sebagai skrip di dalam CTxOuts)
> 6. nilai output yang dihabiskan oleh input ini (8 byte little endian)
> 7. nUrutan input (4 byte little endian)
> 8. hashOutputsHash (hash 32 byte)
> 9. hashOutputs (hash 32 byte)
> 10. nLocktime transaksi (4 byte little endian)
> 11. jenis signature sighash (4 byte little endian)

<center>Diagram 16. Bidang Preimage Hash Tanda Tangan Radiant.</center>

Ini berguna karena keluaran saudara lainnya tidak perlu disertakan dan hash dapat digunakan untuk keluaran yang tidak menarik. Masih ada warna referensi push sehingga kita dapat menegaskan apakah keluaran lainnya berisi warna yang valid, tetapi tanpa memerlukan skrip lengkap untuk didorong.

## Pola Desain Kontrak

Dengan konstruksi `OP_PUSHINPUTREF` dan TxId Versi 3, kami berada dalam posisi untuk menentukan berbagai pola desain kolaborasi kontrak. bersama-sama pola ini akan digunakan dalam emulasi akun, non-fungible tokens (NFTS), fungible tokens (FTs) dan program lainnya.

### Token Tidak Sepadan (NFT)

> <b>Definisi:</b> "Token Non-Fungible" adalah objek yang diidentifikasi secara unik di mana properti esensialnya dilestarikan

Kami menghadirkan pola desain yang sederhana namun kuat yang disebut Non-Fungible Token (NFT). Program akan mengenali ini dengan nama lain yang disebut objek _Singleton_. NFT, atau Singleton, menjamin bahwa hanya satu instance dari suatu objek yang dapat ada dan unik diidentifikasi oleh identitas persisten yang stabil. Sebagian besar penggunaan istilah _Non-Fungible Token_ berpusat di sekitar _koleksi digital_, namun tidak harus demikian &mdash; alasan untuk fokus itu berkaitan dengan biaya gas yang tinggi pada Ethereum dan sifat spekulatif dari karya seni digital.

Dalam blockchain Radiant, kami menggunakan istilah _Non-Fungible Tokens_ untuk merujuk pada objek yang dapat diidentifikasi secara unik, atau _koin berwarna_, yang mempertahankan beberapa properti penting selain dapat dilacak secara jelas melalui blockchain. Ini adalah blok bangunan dasar dan pola desain yang akan muncul dalam kontrak yang lebih kompleks, dan di sini kami menyajikan konstruksi yang sederhana namun sangat kuat di bawah ini untuk memulai.

![Non-Fungible Token Design Overview](images/a17.jpeg)
<center>Diagram 17. Tinjauan Desain Token yang Tidak Dapat Disepadankan.</center>

<br/>

![Check back parent](images/a18.jpeg)
<center>Diagram 18. Desain Output Token Non-Fungible</center>

<br/>

Non-Fungible Token Pseudo-code:
```
contract NFT {

    // Asset identifier
    bytes assetId;
    
    // Current owner is the second push data
    Ripemd160 currentOwnerAddress;      

    public function unlock(
        SigHashPreimage txPreimage, 
        bytes outputSats, 
        bytes newOwnerAddress, 
        bool isMelt, 
        Sig senderSig, 
        PubKey unlockKey
    ) {
        require(hash160(unlockKey) == this.currentOwnerAddress);
        require(checkSig(senderSig, unlockKey));
        
        // Initial assetId is 36-bytes nulls(0x00 bytes)
        bytes actAssetId = (this.assetId == num2bin(0, 36) ? 
            txPreimage[ 68 : 104 ] : this.assetId);
        
        bytes lockingScript = SigHash.scriptCode(txPreimage);
        
        // The default usage is to update/transfer
        if (!isMelt) {
            require(
                hash256(
                    outputSats +

                    // Define length of output
                    b'fd' + num2bin(len(lockingScript, 2)) +
                    
                    // OP_PUSHINPUTREF <assetId>
                    b'd0' + actAssetId +
                    
                    // New owner (20 bytes)
                    b'14' + newOwnerAddress +    

                    // OP_DISALLOWPUSHINPUTREFSIBLING <assetId>       
                    b'd3' + actAssetId + 

                    // Get entire locking script after the push vars 
                    // 95 = 1+36 + 1+20 + 1+36
                    lockingScript[95 : ]                
                )
                ==
                // Compare to HashOuts
                txPreimage[len(txPreimage) - 40 : len(txPreimage) - 8] 
            );
        } else {
            // Melt the NFT back and destroy the reference

            // Use OP_DISALLOWPUSHINPUTREF and 
            // OP_DISALLOWPUSHINPUTREFSIBLING 
            // to prohibit the reference from being passed along
            
            require(
                hash256(
                    // Hardcode len '4b' is 57 bytes (1 + 1 + 36 + 1 + 36)
                    // 'd2' is OP_DISALLOWPUSHINPUTREF
                    // 'd3' is OP_DISALLOWPUSHINPUTREFSIBLING
                    b'00000000000000004b6ad2' + activeAssetId + 
                    b'd3' + activeAssetId 
                )
                ==
                // Compare to HashOuts
                txPreimage[len(txPreimage) - 40 : len(txPreimage) - 8]
            );  
        }
        require(Tx.checkPreimageOpt_(txPreimage));
    }
}

```
<center>Diagram 19. Pseudocode Token yang Tidak Dapat Disepadankan</center>

### Akun

> <b>Definition:</b> An "Account" is an object that manages a wallet balance while maintaining an addressable stable unique identifier. 

Salah satu kesulitan utama dalam bekerja dengan blockchain berbasis UTXO adalah tidak ada konsep tingkat protokol "saldo dompet", dan sebagai gantinya penyedia infrastruktur dan layanan dompet menyajikan saldo ringkasan yang berasal dari nilai total semua keluaran individu yang dikendalikan oleh a kunci. Blockchain berbasis akun juga menyederhanakan jenis masalah dan kontrak tertentu, tetapi mengorbankan kinerja dan privasi untuk mencapai tujuannya.

Kami menghadirkan pola desain sederhana untuk meniru akun menggunakan satu atau lebih keluaran yang memberi pengguna dan pengembang pengidentifikasi unik yang stabil di seluruh transaksi di blockchain. Itu dibangun menggunakan pola desain Token Non-Fungible, dan menunjukkan bahwa blockchain berbasis UTXO dilengkapi dengan sempurna untuk meniru akun dengan tingkat kontrol yang sama, tetapi dengan karakteristik kinerja yang jauh lebih tinggi.

Ingatlah bahwa dalam pola desain Token Non-Fungible, pengidentifikasi stabil _ContractId_ (juga terkadang disebut sebagai _AssetId_) berasal dari titik transaksi pencetakan. ContractId yang sama digunakan sebagai pengidentifikasi akun publik dan dapat diperlakukan sebagai saldo dompet. Di bawah ini kami sajikan kode semu untuk kontrak pintar yang mengimplementasikan semua metode yang terkait dengan akun: setor, tarik, ubah Pemilik, dan tutup akun. 

```
contract Account {

    bytes assetId;
    Ripemd160 currentOwnerAddress;
    bytes disallowAssetIdNotUsed;
 
    static function createSingletonOutput(
        SigHashPreimage txPreimage, 
        int amount, 
        bytes assetId,
        bytes address
    ): bool {
        bytes activeAssetId = (assetId == num2bin(0, 36) ? 
            txPreimage[ 68 : 104 ] : assetId);
        bytes lockingScript = SigHash.scriptCode(txPreimage);
        require(amount > 0);
        require(
                hash256(
                    // Add the deposit amount to the existing balance
                    num2bin(SigHash.value(txPreimage) + amount, 8) +
                    
                    b'fd' + num2bin(len(lockingScript), 2) +

                    // OP_PUSHINPUTREF <assetId>          
                    b'd0' + activeAssetId +    

                    // Address/owner (20 bytes)     
                    b'14' + address +

                    // OP_DISALLOWPUSHINPUTREFSIBLING <assetId>               
                    b'd3' + assetId +    

                    // Get entire locking script after the push vars           
                    // 95 = 1+36 + 1+20 + 1+36
                    lockingScript[95 : ]            
                )
                ==
                txPreimage[len(txPreimage) - 40 : len(txPreimage) - 8] // HashOuts
        );
        require(Tx.checkPreimageOpt_(txPreimage));
        return true;
    }

    // Deposit to account
    // Anyone can spend this input and deposit funds into 
    // the account, but only the owner can withdraw funds.
    public function deposit(
        SigHashPreimage txPreimage, 
        int amount
    ) {
        require(amount > 0);

        require(
            Account.createSingletonOutput(
                txPreimage, 
                SigHash.value(txPreimage) + amount, 
                this.assetId, 
                this.currentOwnerAddress
            )
        );
    }

    // Withdraw from account
    // The current owner can withdraw from the account
    // via any other outputs.
    public function withdraw(
        SigHashPreimage txPreimage, 
        int amount,
        Sig senderSig, 
        PubKey unlockKey
    ) {
        require(hash160(unlockKey) == this.currentOwnerAddress);
        require(checkSig(senderSig, unlockKey));

        require(
            Account.createSingletonOutput(
                txPreimage, 
                SigHash.value(txPreimage) - amount,
                this.assetId, 
                this.currentOwnerAddress
            )
        );
    }

    // Change the account owner
    // The current owner can assign the account to another 
    // address owner
    public function changeOwner(
        SigHashPreimage txPreimage, 
        bytes newOwnerAddress, 
        Sig senderSig, 
        PubKey unlockKey
    ) {
        require(hash160(unlockKey) == this.currentOwnerAddress);
        require(checkSig(senderSig, unlockKey));
        
        require(
            Account.createSingletonOutput(
                txPreimage, 
                SigHash.value(txPreimage), 
                this.assetId, 
                newOwnerAddress
            )
        );
    }

    // Close the account
    // The current owner of the account can permanently close
    // the account and withdraw any tokens via other outputs
    public function close(
        SigHashPreimage txPreimage, 
        Sig senderSig, 
        PubKey unlockKey
    ) {
        require(hash160(unlockKey) == this.currentOwnerAddress);
        require(checkSig(senderSig, unlockKey));
        
        bytes activeAssetId = (this.assetId == num2bin(0, 36) ?
            txPreimage[ 68 : 104 ] : this.assetId);
        
        bytes lockingScript = SigHash.scriptCode(txPreimage);

        // Ensure one of the outputs is unspendable OP_RETURN 
        // and uses the OP codes to prohibit passing on the 
        // reference.
        // OP_DISALLOWPUSHINPUTREF and 
        // OP_DISALLOWPUSHINPUTREFSIBLING which effectively 
        // means no output may contain the reference anymore, 
        // thereby ending the ability to carry on the assetId 
        // anywhere else forever.
        require(
            hash256(
                b'00000000000000004b6ad2' + activeAssetId + 
                // Hardcode len '4b' is 57 bytes (1 + 1 + 36 + 1 + 36)
                b'd3' + activeAssetId 
            )
            ==
            // HashOuts
            txPreimage[len(txPreimage) - 40 : len(txPreimage) - 8] 
        );
        require(Tx.checkPreimageOpt_(txPreimage));
    }
}
```
<center>Diagram 20. Akun Kodesemu kontrak </center>

### Fungible Tokens (FT)

Pola desain Fungible Token memungkinkan kelas atau jenis objek yang sama memiliki lebih dari satu kuantitas. Token yang dapat dipertukarkan dapat digabungkan bersama, dengan nilainya dijumlahkan menjadi keluaran baru, atau keluaran dapat dibagi menjadi dua atau lebih keluaran di mana jumlah total keluaran sama dengan jumlah nilai masukan. Pola desain ini berguna untuk mensimulasikan poin loyalitas, token, dan lainnya.

Kami hadirkan solusinya:

```
contract SuperAssetR201 {
    // Do NOT provide a constructor as that will add unnecessary OP_0 OP_0 to the beginning of the contract
    bytes assetId;                      // Asset identifier
    Ripemd160 currentOwnerAddress;      // Current owner is the second push data
    // Notice that "disallowAssetIdNotUsed" is not used below. The reason is that we save space and also it should always be same as assetId
    bytes disallowAssetIdNotUsed;       // Disallow Asset from being used in any other output
    static const int MAX_RECEIVE = 6;
  
    static function buildOutputVector(
        int amount, 
        bytes assetId,
        bytes address,
        bytes outputScriptLen,
        bytes lockingScriptCodePart
    ): bytes {
        return 
            num2bin(amount, 8) +
            hash256(
                outputScriptLen +
            
                // OP_PUSHINPUTREF <assetId>          
                b'd0' + assetId +    

                // Address/owner (20 bytes)     
                b'14' + address +

                lockingScriptCodePart     
            ) +
            // One color for the output
            b'01000000' +
            hash256(assetId);
    }

    public function mint(SigHashPreimage txPreimage, int amount) {
        require(amount > 0);
        require(this.assetId == num2bin(0, 36));
        bytes lockingScript = SigHash.scriptCode(txPreimage);
        require(
                hash256(
                    num2bin(amount, 8) +
                    
                    b'fd' + num2bin(len(lockingScript), 2) +

                    // OP_PUSHINPUTREF <assetId>          
                    b'd0' + txPreimage[68 : 104]+    

                    // Address/owner (20 bytes)     
                    b'14' + this.currentOwnerAddress +

                    // Get entire locking script after the push vars           
                    // 95 = 1+36 + 1+20
                    lockingScript[58 : ]            
                )
                ==
                txPreimage[len(txPreimage) - 40 : len(txPreimage) - 8] // HashOuts
        );
        require(Tx.checkPreimageOpt_(txPreimage));
    }

    public function transfer(SigHashPreimage txPreimage, Ripemd160[6] recipients, int[6] amounts, bytes otherOutputs, Sig senderSig, PubKey unlockKey) {
        require(hash160(unlockKey) == this.currentOwnerAddress);
        require(checkSig(senderSig, unlockKey));

        int expectedRefColorSum = 1337;         // Placeholder for OP_INPUTREFVALUESUM
        int actualAccumulatedRefColorSum = 0;   // Used for counting the sum of the colors
        bool break = false;
        bytes expectedOutputVector = b'';
        bytes lockingScript = SigHash.scriptCode(txPreimage);
        // Length of the output script
        bytes outputScriptLen = b'fd' + num2bin(len(lockingScript), 2);
        bytes lockingScriptCodePart = lockingScript[58 : ];
        loop (MAX_RECEIVE) : i {
            if (!break) {
                if (amounts[i] <= 0) {
                    break = true;
                } else {
                    // There is a valid recipient...
                    // Get entire locking script after the push vars           
                    // 58 = 1+36 + 1+20
                    expectedOutputVector += SuperAssetR201.buildOutputVector(amounts[i], this.assetId, recipients[i], outputScriptLen, lockingScriptCodePart);
                    actualAccumulatedRefColorSum += amounts[i];
                }
            }
        }
        require(expectedRefColorSum > 0 && expectedRefColorSum == actualAccumulatedRefColorSum);
        require(
            hash256(expectedOutputVector + otherOutputs)
            ==
            // hashOutputsHashes
            txPreimage[len(txPreimage) - 72 : len(txPreimage) - 40] 
        );
    }
 
    public function melt(SigHashPreimage txPreimage, Sig senderSig, PubKey unlockKey) {
        require(hash160(unlockKey) == this.currentOwnerAddress);
        require(checkSig(senderSig, unlockKey));
        // Ensure one of the outputs is unspendable OP_RETURN and uses the OP codes to prohibit passing on the reference
        // OP_DISALLOWPUSHINPUTREF and OP_DISALLOWPUSHINPUTREFSIBLING which effectively means no output may contain
        // the reference anymore, thereby ending the ability to carry on the assetId anywhere else forever.
        require(
            hash256(
                // Hardcode len '4b' is 57 bytes (1 + 1 + 36 + 1 + 36)
                b'00000000000000004b6ad2' + this.assetId +  b'd3' + this.assetId
            )
            ==
            txPreimage[len(txPreimage) - 40 : len(txPreimage) - 8]  
        );  
        require(Tx.checkPreimageOpt_(txPreimage));
    }
}

```

# Detail Jaringan Blockchain

Radiant adalah sistem aset digital peer-to-peer dengan penskalaan tak terbatas sebagai blockchain berbasis UTXO dengan semua fleksibilitas dan kekuatan blockchain berbasis akun.

> <b>Network Name:</b> Radiant<br/>
> <b>Network Abbreviation:</b> RXD<br/>
> <b>Mining Algorithm:</b> SHA512/256 Proof-of-work<br/>
> <b>Block Time:</b> 5 menit<br/>
> <b>Initial Block Size:</b> 128 MB, dirancang untuk mencapai 10GB+<br/>
> <b>Block Reward Schedule:</b> 50,000 RXD per blok<br/>
> <b>Block Reward Halvening:</b> 2 tahun<br/>
> <b>Maximum Supply:</b> 21,000,000,000 RXD<br/>
> <b>Decimal Places:</b> 8<br/>
> <b>Launch Date:</b> 2022-06-21 02:42 UTC<br/>

# Kesimpulan
 
Kami telah mengusulkan sistem manajemen aset digital tanpa mengandalkan kepercayaan. Kami mulai dengan konstruksi blockchain dasar dari koin yang terbuat dari tanda tangan digital, yang memberikan kontrol kepemilikan yang kuat. Dari aturan dan insentif yang diperlukan, kami memperkenalkan dua metode baru untuk mengautentikasi dan melacak aset digital dalam ruang dan waktu O(1) yang konstan. Kedua metode secara independen menyediakan sistem bukti induksi umum yang dapat menyandikan konfigurasi aset digital apa pun yang memungkinkan. Sistem Turing Lengkap di dalam dan melintasi batas transaksi, dengan skala tak terbatas, dan tidak pernah membutuhkan lapisan sekunder. Selain itu, kami telah menyajikan tiga pola desain kontrak: Non-Fungible Token (NFT), Fungible Token (FT), dan Akun yang meniru blockchain berbasis akun, menggunakan model pemrosesan berbasis UTXO. Radiant adalah desain terobosan yang memberikan manfaat kinerja dan paralelisme dari blockchain output transaksi yang tidak terpakai (UTXO), dan dengan kecanggihan pemrograman dari blockchain berbasis akun, dengan tetap mempertahankan biaya yang sangat rendah dan skala yang tidak terbatas.

# References

[1] Satoshi Nakamoto, "Bitcoin: A Peer-to-Peer Electronic Cash System" https://bitcoin.org/bitcoin.pdf, 2009.
 
