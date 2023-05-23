# Radiant: Sistem Kasugihan Aset Digital Peer-to-Peer

Kabeh Pangembang Radiant

11 Agustus, 2022

radiantblockchain.org

**Abstrak.** Radiant Network punika sistem aset digital peer-to-peer
bisa ijol-ijolan tandha kanthi cara langsung tanpa liwat pesta tengah. 
Protokol asline Bitcoin [1] nyedhiyakake apa sing dibutuhake Kanggo gawe sistem kas
elektronik peer-to-peer, nanging Ora duweke kabisan Kanggo ngecek sajarah 
transaksi lan mulane iku, ora saget digunakake Kanggo validasi aset digitale.
Tandha digital lan wates output menehi bagean saka solusi, nanging 
entuk manfaat utama wis ilang Yen pesta sing dipercaya _ Isih dibutuhake Kanggo 
validasi aset digital . Serupa karo Bitcoin, jaringan Radiant mbutuhake 
struktur minimal , lan cathetan wektu transaksi kanggo ing rante bukti Kerja adhedhasar 
hashes medium lumaku . We introduce kalih teknik Kanggo 
validasi aset digital : referensi unik lan sistem bukti induksi objektif sing umum
loro-lorone operate ing papan lan wektu O( 1) konstanta . Iku bisa Kanggo 
nyipta output karo cara apa wae , tanpa kurban ciri 
paralelisme inheren lan kinerja _ saka arsitektur adhedhasar output transaksi _
Ora digunakake (UTXO). amarga_ _ iku , pangguna saget metu lan melu 
bali karo Jaringan sumringah ing bakal ati lan yakin bakal integritas lan keasliane 
aset digitale . 


# 1. Pambuka

Dagang karo blockchain, utawa teknologi ledger digital (DLT), ing akeh kasus gumantung marang sing ngetokake sekuritas lan kustodian sing dadi pihak sing dipercaya kanggo otentikasi aset digital. Nalika sistem kasebut bisa digunakake kanthi becik kanggo transaksi kayata pembayaran elektronik, dheweke isih nandhang kelemahane. -model adhedhasar kanggo majeng
nggunakake. Mesin Virtual Ethereum (EVM) berbasis Blockchain [2] fleksibel banget kanggo kabeh jinis program, nanging biaya sing dhuwur nggawe aplikasi micropayment ora praktis. Sing dibutuhake yaiku sistem pembayaran elektronik sing bisa digunakake kanggo biaya sing murah, dhuwur- kinerja sistem manajemen aset digital, lan kemampuan pemrograman tingkat
nglakokaké. Ing makalah iki, kita ngusulake solusi kanggo masalah skala lan kontrak blokchain nggunakake rong teknik anyar sing nyedhiyakake referensi unik lan sistem bukti induksi umum, sing ndadekake program Turing Complete [3] ngliwati wates transaksi bisa. Sistem sing diusulake didesentralisasi, nggunakake mekanisme konsensus bukti-kerja kaya Bitcoin, nanging kanthi tingkat throughput sing luwih dhuwur, nalika nyedhiyakake keluwesan sing padha karo blockchain berbasis EVM, kanthi biaya sing murah banget.

# 2. Transaksi

Kaya Bitcoin, kita nemtokake koin elektronik minangka seri tandha asta digital. Yen transaksi ing Radiant beda-beda yaiku saben pemilik nransfer dhuwit recehan menyang sabanjure kanthi menehi tandha digital hash saka transaksi sadurunge saliyane parameter input sing dibutuhake kanggo mbukak kunci koin kasebut. Transaksi uga nggawe watesan ngunci output anyar, sing bisa uga kalebu kunci umum saka pemilik sabanjure, ing antarane aturan sing ditemtokake pangguna liyane.

![Diagram 1. Radiant Transactions.](images/w2.jpeg)
>**Diagram 1.** Transaksi Radiant. 
<br/>

Kanggo verifikasi manawa mbuwang kaping pindho ora kedadeyan, kita nggunakake server timestamp sing disebarake, nggunakake sistem bukti kerja adhedhasar hash kanggo nyetel riwayat kanonik kanggo nemtokake transaksi sing teka dhisik. 
Transaksi diatur dadi blok. Miturut konvensi, transaksi pisanan, sing diarani "transaksi berbasis koin", ing blok minangka transaksi khusus sing miwiti koin anyar sing diduweni dening panyipta blok kasebut. Blok dirantai bebarengan lan ngatur transaksi dadi Merkle Tree [4].
Kabeh transaksi, kajaba sing pisanan, kudu ngrujuk marang transaksi sadurunge sing mbentuk grafik asiklik terarah (DAG) ing ngendi kabeh dhuwit recehan pungkasane nyambung menyang paling ora siji transaksi tartamtu ing wiwitan blok.

![Diagram 2. Block Structure; transactions are organized into a Merkle Tree.](images/w3.jpeg)
>**Diagram 2.**  Struktur Blok; transaksi diaturke menyang Wit Merkle.
<br/>

Masalah karo desain iki, ing konteks aset digital, mung ana siji jinis koin, utawa aset digital, lan ora ana konsep koin sing ditemtokake pangguna (utawa jinis aset digital). Desain bisa uga cukup kanggo transaksi kaya pembayaran elektronik ing unit
akun nyata, nanging ora langsung cocok kanggo nggunakake liyane jinis dhuwit recehan utawa aset digital. Solusi umum yaiku ngenalake layanan kayata indeksasi transaksi sing ngawasi transaksi kanggo urutan data tartamtu kanggo menehi tandha nggawe aset digital. Masalah karo solusi iki yaiku gumantung marang perusahaan sing mbukak layanan kasebut, kanthi keaslian aset digital sing kudu dipercaya, kaya layanan liyane ing web.
We needed cara kanggo kedhaftar kanggo nduduhake nggawe jinis tartamtu saka duwit receh, nanging ora gumantung ing layanan dipercaya kanggo digunakake kanggo presentation data.
 
# 3. Aset Digital

Kita nemtokake koin elektronik khusus, utawa aset digital, minangka seri tandha asta digital.
Aset digital minangka jinis koin sing ditemtokake pangguna nggunakake token transaksi khusus, sing diarani "transaksi basis aset", kanggo nggawe utawa nggawe aset digital. Mirip karo transaksi coinbase, kang inject dhuwit recehan anyar menyang sistem, transaksi assetbase werna utawa tandha dhuwit recehan elektronik karo pengenal 36-bait unik kanggo umur.
Kostume e-coin dilapisi ing ndhuwur jinis duwit receh dhasar lan bisa digunakake kanthi cara sing padha. Transaksi basis aset bisa katon ing ngendi wae ing blok kasebut lan bisa ngetrapake aturan lan watesan khusus apa wae sing diputusake sadurunge.

![Diagram 3. Transactions representing user-defined coin types &mdash; or digital assets.](images/w4.jpeg)
>**Diagram 3.** Transaksine kuwi makili jinis duwit receh pangguna-ditetepake &mdash; utawa digital
asset 
<br/>

Kanggo nindakake, kita kudu nggawe pengenal unik sing stabil lan mekanisme transaksi kanggo nglacak keaslian jinis koin (aset digital). Pangguna sistem kudu duwe bukti yen jinis koin tartamtu ora palsu lan kanthi akurat nggambarake aset digital kasebut.

![Diagram 4. Custom user-defined coin types are defined from a special mint transaction. A unique identifier is used to classify the coin type.](images/w5.jpeg)
>**Diagram 4.** Jinis koin khusus sing ditemtokake pangguna ditemtokake saka transaksi mint khusus. Pengenal unik digunakake kanggo nggolongake jinis koin.
<br/>

# 4.  Identifier Seng Unik

Kanggo aplikasi pengenal unik kanggo jinis duwit receh, kita nggunakake transaksi token khusus, disebut "transaksi basis aset", kang tumindak minangka wiwitan (mint) saka chain teken digital. Tinimbang mbutuhake struktur data anyar kanggo pengenal unik, kita nggunakake maneh pengenal transaksi lan indeks output, disebut "outpoint", minangka pengenal unik kanggo jinis duwit receh. Mesthekake yen outpoints (36-bait) iku acak lan global unik.

Instruksi pemrograman, sing diarani `OP_PUSHINPUTREF`, digunakake kanggo masang referensi menyang output. Instruksi kasebut nampa persis siji parameter 36-bait sing kudu cocog karo 1) titik metu saka salah sawijining output sing digunakake, utawa 2) nilai 36-bait sing padha wis katon ing `OP_PUSHINPUTREF` sing wis ditemtokake ing salah sawijining output sing digunakake. Cara mung kanggo nilai tartamtu kanggo katon ing output transaksi liwat sawetara transaksi leluhur, Nilai cocog titik metu saka transaksi basis aset minting dhisikan. Transaksi sing nemtokake nilai sing ora cocog karo kondisi apa wae ora sah.

![Diagram 5. Unique identifiers are initialized by matching an outpoint of one of the outputs being spent, and then maintained as long as at least one of the outputs being spent contains the same unique identifier in the script body.](images/w6.jpeg)
>**Diagram 5.** Pengenal seng unik diinisialisasi kanthi cocog karo titik metu saka salah sawijining output sing digunakake, lan banjur dijaga anggere paling ora siji output sing ditanggepi ngemot pengenal unik sing padha ing awak naskah.
<br/>

Instruksi pemrograman prasaja iki nyedhiyakake pengenal unik sing bisa digunakake minangka referensi stabil kanggo nggawe aturan lanjut. Contone, macem-macem jinis dhuwit recehan, aset digital,
saiki bisa gumantung ing jinis dhuwit recehan liyane. Amarga kabeh data lokal kanggo transaksi, liwat transaksi input wong tuwa langsung, gampang kanggo klien lan layanan kanggo validasi
keaslian aset digital ing wektu lan papan O (1) konstan, ngindhari kabutuhan layanan sing dipercoyo.

# 5.  Buktine Miturut Induksi

Sampeyan bisa ngasilake pengenal unik kanthi cara alternatif lan uga nyedhiyakake mekanisme kanggo bukti induksi matematika [5] nggunakake algoritma  transaksi hash sing diowahi. Kanthi ngidini skrip input nampa transaksi wong tuwa sing wis dileksanakake, aturan bisa verifikasi manawa wong tuwane, lan simbah, cocog karo syarat aturan kasebut. Masalah ketok iku minangka saben salinan lengkap saka transaksi tiyang sepah ditempelake bledosan ukuran eksponensial ana lan ngalangi nggunakake praktis saka technique. Apa sing dibutuhake yaiku cara kanggo ngompres transaksi, supaya struktur data ukuran tetep bisa digunakake kanggo ngasilake hash transaksi, tinimbang mbutuhake isi transaksi lengkap.

![Diagram 6. Full parent transaction validation, mathematical induction proof by embedding the full parent transactions into the inputs resulting in exponential transaction size increase.](images/w7.jpeg)
>**Diagram 6.** Validasi transaksi induk seng lengkap, bukti induksi matematika kanthi ngetik transaksi induk seng lengkap menyang input sing nyebabake paningkatan eksponensial ing ukuran transaksi.
<br/>

Kita bisa nindakake iki kanthi ngowahi algoritma hash transaksi sing digunakake ing Bitcoin, ing ngendi intisarine sha-256 seng gandha diitung saka transaksi serial, menyang versi anyar sing pisanan ngringkes isi transaksi kanggo nurunake hash. Kita ngenalake versi 3 saka algoritma hash transaksi, kanggo mbedakake saka versi 1 lan 2 sing digunakake ing Bitcoin. Proses kasebut minangka ciri saben lapangan, utawa komponen transaksi, dadi hash perantara, sing bisa digunakake minangka input ukuran tetep lan kanthi mangkono ngindhari pertumbuhan ukuran transaksi eksponensial.

Kita nggunakake struktur data 112-bait ing ngisor iki, tinimbang bita transaksi serialized lengkap, kang siji pindho sha-256 hash kanggo pungkasanipun njaluk hash transaksi.

Pragambar Hash Kolom Transaksi Versi 3:
> 1. nVersion(=3) transaksi (4 byte little endian)
> 2. nTotalInputs (4 byte little endian)
> 3. hashPrevoutInputs (hash 32 byte)
> 4. hashSequence (hash 32 byte)
> 5. nTotalOutputs (4 byte little endian)
> 6. hashOutputHash (hash 32 byte)
> 7. nLocktime transaksi (4 byte little endian)

Nggunakake algoritma hash transaksi kanggo versi transaksi ke 3 (ketigo), kita bisa nampilaké transaksi tiyang sepah lan simbah ing saben langkah saka bukti prabawa matématika kanggo nyegah Tambah ing ukuran transaksi, lan bisa aplikasi sembarang aturan yen perlu.

![Diagram 7. Compressed parent transaction validation, mathematical induction proof by embedding the transaction hash version 3 preimage data-structure of the parent and grand-parent to enforce arbitrary rules and constraints.](images/w8.jpeg)

>**Diagram 7.** Validasi transaksi induk sing dikompres, bukti induksi matematika kanthi nanem struktur data preimage hash transaksi versi 3 saka wong tuwa lan simbah kanggo ngetrapake aturan lan kendala sing sewenang-wenang
<br/> 
 
# 6. Jaringane

Topologi jaringan minangka grafik sing meh lengkap, ing ngendi saben Node Pertambangan disambungake karo saben Node Pertambangan liyane. Langkah-langkahe kanggo mbukak jaringane padha karo Bitcoin, kanthi sawetara beda kanggo macem-macem jinis simpul: Node Pertambangan, Node Agen, Node Arsip. Node Pertambangan minangka penerbit blok aktif lan njaga konsensus karo kabeh simpul liyane, Node Arsip iku nglayani riwayate data pemblokirane, lan Node Agen dirancang kanggo nyaring blok lan nglacak transaksi kapentingan kanggo aplikasi sing dilayani. Arsip lan Agen Node bisa beroperasi ing jaringan peer-to-peer sing padha nanging ora ngasilake blok. Node Non-Mining kayata Arsip lan Agen Node kadhangkala diarani minangka "node seng ngrungokake" kanggo mbedakake peran ing jaringane.
 
![Diagram 8. Mining Nodes are well-connected and build on top of each other's blocks. Archive nodes store complete blocks for historical analysis and bootstrapping purposes. Agent nodes are listener nodes which filter and store transactions to serve clients.](images/w9.jpeg)

>**Diagram 8.** Node Mining disambungake kanthi apik lan dibangun ing ndhuwur blok masing-masing. Node arsip nyimpen blok lengkap kanggo analisis historis lan tujuan bootstrap. Node agen minangka simpul pamireng sing nyaring lan nyimpen transaksi kanggo nglayani klien.
<br/>

Node Pertambangane disambungake kanthi luwih apik menyang grafik sing meh lengkap ing antarane Node Pertambangan liyane. Tugase yaiku mbangun blok saben liyane lan njaga konsensus kanggo sawetara atus blok paling anyar, lan njaga set UTXO kanggo pencegahan belonjo kaping pindho..

Agen Node mung kudu nyimpen subset transaksine, contone jinis koin utawa aset digital seng tartamtu. Malah kanthi pamblokirane gedhe, Agen Node bisa kanthi cepet nyaring transaksine kanthi referensi tartamtu utawa pesenan byte, banjure nyimpen transaksi kasebut kanggo dilayani liwat antarmuka programmer aplikasine. Antarane agen kerja sama, Akar Wit Merkle seng muat hash bisa diumumake sacara umum kanggo transaksi ing saben blok sing cocog karo pola sing wis ditemtokake kanggo menehi sinyal menyang Agen lan konsumen liyane sing transaksi wis diproses Agen.

Node arsip digunakake kanggo nggawe salinan serep kabeh blok kanggo macem-macem aplikasi kalebu gudang data, analytics, lan pembelajaran mesin. Amarga Node Arsip ora langsung melu pertambangan, dheweke ora duwe kinerja wektu nyata lan syarat bandwidth sing padha karo Node Pertambangan utawa Agen.

# 7. Kalkulasine

Kita nimbang skenario ing ngendi jaringan Radiant terus berkembang lan apa sing dibutuhake kanggo syarate pangolahan Node Pertambangan, Arsip lan Agen. Ing comparison, ing wektu nulis, ana kira-kira 83 yuta output transaksi unspent kanggo pamblokiran Bitcoin, total watara 6 GB data dibutuhake kanggo nyegah pindho mbuwang. Mung mbutuhake sawetara atus blok paling anyar disimpen dening Mining Nodes, kanthi blok lawas sing kasedhiya saka Arsip Nodes. Kanggo Agen Nodes, penting kanggo njaga partisi sing cocog kanggo output transaksi sing ora digunakake sing ana gandhengane karo aplikasi sing dilayani, skala minangka fungsi bandwidth lan dudu syarat panyimpenane.

Kanggo tujuan kita, kita nganggep bakal ana blok 3GB saben 5 menit sing bakal diwenehi tandha wektu lan disebarake ing jaringan, utawa kira-kira 20.000 transaksi per detik kanthi ukuran transaksi rata-rata 500 bita, utawa kira-kira 6.000.000 transaksi saben blok. Kita nuduhake manawa kanggo saben jinis simpul, jaringan bisa cukup kanggo nyukupi kabutuhan global. Iki padha karo kira-kira 1 transaksi saben 5 dina kanggo saben 8 milyar wong ing planet iki.

### Node Pertambangane

Node Mining minangka siji-sijine jinis simpul sing dibangun ing ndhuwur blok masing-masing. Kanggo njaga konsensus, cukup kanggo nyinkronake blumbang output transaksi sing ora digunakake (UTXO), lan nahan mung atusan blok pungkasan. Ing wektu nulis, kinerja dhuwur komoditas solid-state drive saged liwat 120.000 IOPS, biaya watara $500 USD kanggo 280 GB, lan mulane bisa nangani bab 20.000 transaksi per detik (assuming saben transaksi duwe 2 input lan 2 output). Ana 2 maca kanggo input, 2 nganyari kanggo input, lan 2 nulis kanggo output anyar: 120.000/6 = 20.000 transaksi/detik.

### Node arsipe

Node arsip nyedhiyakake data blok historis lan cocok kanggo aplikasi machine learning, analytics, lan data warehousing. Arsip Node bisa nglengkapi Bootstrap Mining Nodes lan kanggo mbukak mriksa konsistensi periodik ing set UTXO. Nalika nulis, drive komoditas 18 TB kasedhiya sekitar $350 USD. Yen 3 GB data saben 5 menit padha karo 732 GB kabutuhan panyimpenan data saben dina, utawa watara 22 TB saben sasi. Biaya hardware kanggo setahun yaiku 15 drive, kanthi kapasitas 18 TB, kanthi biaya tambahan $5,000 USD.

### Node Agen

Agent Nodes skala paling gampang saka jinis simpul amarga mung ngolah jinis transaksi sing cocog kanggo aplikasi sing dilayani. Akibaté, Agen Nodes bisa saka server web nganti piranti IoT sing entheng kanthi kemampuan pangolahan lan panyimpenan winates, nalika tetep sepi ing blok 3GB, utawa 20.000 transaksi per detik. Contone, perusahaan bisa uga pengin nglacak panggunaan poin kesetiaan, digawe minangka aset digital, lan mulane mung kudu milih sawetara nganyari transaksi saka saben blok sing cocog karo pengenal unik kanggo jinis koin kasebut.

Nalika nulis, piranti komputasi komersial, Raspberry Pi 4, didol udakara $ 275. Nduweni prosesor quadcore 1,5 GHz lan 4 GB RAM, sing ngidini kanthi cepet nyaring, lan mbuwang transaksi sing ora relevan, kanthi tingkat saka 5.000 transaksi saben inti. Mesthine, iki mung minangka conto babagan carane ngolah blok gedhe, ing aplikasi web sing khas bisa uga ana luwih akeh inti sing kasedhiya.

Kacepetan bandwidth rata-rata ing 25 negara paling dhuwur ngluwihi 100 MBPS, utawa udakara 10 MB/s download, kanthi akeh panyedhiya layanan internet sing nawakake undhuhan tanpa wates. Kebutuhan bandwidth kanggo blok 3GB saben 5 menit sekitar 10MB/s kanthi total 22TB saben wulan. Hierarki Agen Node uga bisa digawe kanggo nyaring total bandwidth syarat kanggo Agen Node kanthi kapasitas bandwidth sing luwih murah.

# 8. Kesimpulane
 
Kita wis ngusulake sistem manajemen aset digital tanpa ngandelake kepercayaan. Kita wes miwiti karo blok bangunan dhasar saka koin sing digawe saka tandha digital, sing nyedhiyakake kontrol kepemilikan sing kuwat. Saka aturan lan insentif sing dibutuhake, kita ngenalake rong cara anyar kanggo keasliane lan nglacak aset digital ing wektu lan papan O (1) konstan. Kaloro cara kasebut kanthi mandiri nyedhiyakake sistem bukti induksi matematika umum sing bisa ngode konfigurasi aset digital. Sistem Turing Lengkap ing lan ngliwati wates transaksi, tanpa perlu lapisan sekunder. Radiant minangka desain terobosan sing nyedhiyakake keuntungan kinerja lan paralelisme saka pamblokiran output transaksi sing ora digunakake (UTXO), nanging kanthi kemampuan kontrak blokade adhedhasar akun adhedhasar Ethereum Virtual Machine (EVM).

# Referensine

[1] Satoshi Nakamoto, URL "Bitcoin: Sistem Uang Elektronik Peer-to-Peer" https://bitcoin.org/bitcoin.pdf, 2009.
 
[2] Vitalik Buterin, "Ethereum: Kontrak Cerdas Generasi Berikutnya dan Platform Aplikasi Terdesentralisasi." URL https://ethereum.org/en/whitepaper/, 2014.

[3] Kontributor Wikipedia. "Turing kelengkapan." Wikipedia, ensiklopedia gratis. Wikipedia, Ensiklopedia Gratis, URL https://en.wikipedia.org/wiki/Turing_completeness, 21 Juli 2022

[4] RC Merkle, "Protokol untuk kriptosistem kunci publik," Dalam Proc. 1980 Symposium on Security and Privacy, IEEE Computer Society, halaman 122-133, April 1980.

[5] Britannica, T. Editor Ensiklopedia. "induksi matematika." Ensiklopedia Britannica, URL https://www.britannica.com/science/mathematical-induction, 2018
