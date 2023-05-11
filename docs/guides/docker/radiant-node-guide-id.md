# Jalankan Docker Node Radiant 

## Menginstal Docker:
Proses pemasangan mesin docker tergantung pada sistem operasi Anda.
Lihat https://docs.docker.com/engine/install/ dan cari instruksi untuk OS Anda.

## Menarik Docker image:
Anda dapat menelusuri https://hub.docker.com/ untuk gambar yang mungkin berguna bagi Anda. Pada saat penulisan, ada dua opsi untuk menjalankan node Radiant: hanya node dan node dengan elektrum.

Gambar hanya untuk node: https://hub.docker.com/r/radiantcommunity/radiant-node.

Gambar untuk node + elektrum: https://hub.docker.com/r/radiantcommunity/electrumx_radiant_node.

Jika Anda ingin menjalankan versi elektrum, tidak perlu juga menjalankan wadah node murni.


Di bawah perintah Docker Anda dapat melihat perintah untuk mengambil gambar sehingga Anda menyimpannya secara lokal.
Jika Anda tahu gambar apa yang Anda inginkan, tidak perlu melakukannya secara manual.
Saat kami menjalankan gambar dan Docker tidak dapat menemukannya secara lokal, ia akan mencarinya di Docker Hub.

## Menjalankan Kontainer:
Perintah untuk memulai kontainer  node adalah:

```
sudo docker run --name radiantnode -itd radiantcommunity/radiant-node
```

![](https://github.com/Bactol-git/Radiant-node/blob/main/img/run.png)

Anda mungkin tidak perlu sudo jika Anda telah memberikan hak docker sudo.
Tag -itd adalah untuk menjalankan wadah secara interaktif, dengan tty (terminal) dan dilepas (background).
Bit terakhir adalah referensi ke gambar yang ingin Anda jalankan.

Tag nama bersifat opsional, tetapi membuatnya lebih mudah saat kita menentukannya saat kita ingin memeriksa penampung dan saat kita menghentikannya.

Jika ini adalah pertama kalinya Anda menjalankan perintah dan tidak memiliki gambar secara lokal, Anda akan melihat proses pengunduhan. Ini hanya untuk saat Anda mengambil gambar baru. Kali kedua Anda menjalankannya akan jauh lebih cepat karena Anda sudah memiliki gambarnya. Ketika Anda menjalankan perintah, Anda akan melihat hash, dan itu berarti wadah telah dimulai.

Perintah untuk memulai wadah node + elektrum adalah:

```
sudo docker run --name radiantelectrum -itd radiantcommunity/electrumx_radiant_node
```

## Memeriksa Kontainer:
Anda dapat menjalankan ini untuk melihat kontainer apa yang sedang berjalan:

```
sudo docker ps
```

![](https://github.com/Bactol-git/Radiant-node/blob/main/img/ps.png)

dan Anda dapat menjalankan ini untuk melihat semua kontainer yang telah dijalankan:

```
sudo docker ps -a
```

![](https://github.com/Bactol-git/Radiant-node/blob/main/img/ps-a.png)

Mengakses kontainer untuk memeriksa dan memecahkan masalah:
Kita dapat mengakses terminal kontainer yang sedang berjalan untuk memeriksa dan memecahkan masalah menggunakan perintah ini:

```
sudo docker exec -it radiantnode sh
```

Ini akan membuka terminal di kontainer dan kita dapat menjalankan perintah untuk melihat bahwa semuanya berjalan seperti yang diharapkan. Anda akan melihat simbol # yang berarti kita berada di terminal node. Sekarang kita dapat menjalankan:

```
radiant-cli -getinfo 
```

untuk melihat bahwa node kami sedang berjalan.
Anda dapat melihat perintah berguna lainnya untuk dijalankan pada node di panduan node radiant (https://radiant4people.com/guides/node/compile/#useful-commands).

![](https://github.com/Bactol-git/Radiant-node/blob/main/img/radiant-cli.png)

To kembali keluar dari wadah cukup gunakan jalankan``` exit ``` di dalam kontainer.

Kami sekarang memiliki node radian fungsional dan berjalan, tetapi mengapa berhenti di situ?
Kita dapat dengan mudah menjalankan banyak node sekaligus. Mulai saja node lain menggunakan image  yang sama (tetapi nama berbeda).
Ini akan menelurkan kontainer baru dengan node baru berjalan. Misalnya:

```
sudo docker run --name radiantnode2 -itd radiantcommunity/radiant-node
```

Kita sekarang dapat menggunakan

```
sudo docker ps
```

untuk melihat bahwa kami memiliki dua node berjalan.

## Hentikan Kontainer:
Saat tiba waktunya untuk menghentikan kontainer, Anda cukup menjalankan: 

```
sudo docker stop radiantnode
```

"radiantnode" adalah nama yang kami tentukan saat kami memulai kontainer  pertama. Untuk menghentikan kontainer kedua kami mulailah:

```
sudo docker stop radiantnode2
```

Untuk menghentikan node + electrum:

```
sudo docker stop radiantelectrum
```
