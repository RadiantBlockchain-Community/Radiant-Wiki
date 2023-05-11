---
hide:
  - toc
---

# KOMPILASI NODE RADIANT DENGAN UBUNTU 22.04
Panduan kecil untuk mengkompilasi node Radiant dan menggunakan perintahnya

## Github
- **Kode sumber node radiant:** [https://github.com/RadiantBlockchain/radiant-node](https://github.com/RadiantBlockchain/radiant-node)
- **Panduan asli untuk mengkompilasi di Ubuntu:** [https://github.com/RadiantBlockchain/radiant-node/blob/master/doc/build-unix-deb.md](https://github.com/RadiantBlockchain/radiant-node/blob/master/doc/build-unix-deb.md)
--------------------------------------------------------------------------------------------

## Daftar pustaka untuk dikompilasi

**Pustaka dan aplikasi yang diperlukan untuk mengkompilasi node:**
```
sudo apt-get install build-essential cmake git libboost-chrono-dev libboost-filesystem-dev libboost-test-dev libboost-thread-dev libevent-dev libminiupnpc-dev libssl-dev libzmq3-dev help2man ninja-build python3 libdb-dev libdb++-dev
```
![](https://raw.githubusercontent.com/Antares-ES/Radiant-Guides/main/Compile-Node/img/01-UBUNTU-22_04_install-dependencies.png)

## Unduh sumber node

**Unduh kode sumber node radiant untuk dikompilasi:**
```
git clone https://github.com/radiantblockchain/radiant-node.git
```
![](https://raw.githubusercontent.com/Antares-ES/Radiant-Guides/main/Compile-Node/img/02-UBUNTU-22_04_download-node-github.png)

**Setelah Anda mengunduh kode sumber, masukkan folder yang dihasilkan, buat folder build dan masukkan di dalamnya**
```
cd radiant-node/
mkdir build
cd build
```
![](https://raw.githubusercontent.com/Antares-ES/Radiant-Guides/main/Compile-Node/img/03-UBUNTU-22_04_create-dir.png)

## Opsi kompilasi

**Dua opsi untuk dikompilasi (QT tidak berfungsi dengan baik):**

- No QT (recommend)
```
cmake -GNinja .. -DBUILD_RADIANT_QT=OFF
```

- No wallet, no QT
```
cmake -GNinja .. -DBUILD_RADIANT_WALLET=OFF -DBUILD_RADIANT_QT=OFF
```


![](https://raw.githubusercontent.com/Antares-ES/Radiant-Guides/main/Compile-Node/img/04-UBUNTU-22_04_preparing-no-qt.png)
--------------------------------------------------------------------------------------------

**Setelah semuanya dikonfigurasi, lanjutkan untuk mengkompilasi dengan ninja dan tunggu beberapa menit.**
```
ninja
```
![](https://raw.githubusercontent.com/Antares-ES/Radiant-Guides/main/Compile-Node/img/05-UBUNTU-22_04_compile.png)

**Hasilnya akan dihasilkan di folder src**
src/radiantd	
src/radiantd
src/radiant-cli
src/radiant-wallet


**Untuk menjalankan node dari lokasi manapun, kita dapat menyalin file ke direktori sistem**
```
sudo cp src/radiant* /usr/local/bin/
```
![](https://raw.githubusercontent.com/Antares-ES/Radiant-Guides/main/Compile-Node/img/06-UBUNTU-22_04_copy-build-bin.png)

--------------------------------------------------------------------------------------------

## **Buat file konfigurasi**
```
mkdir ~/.radiant
touch ~/.radiant/radiant.conf
```
![](https://raw.githubusercontent.com/Antares-ES/Radiant-Guides/main/Compile-Node/img/07-UBUNTU-22_04_create-config-node.png)

**Salin data utama ke file konfigurasi**
```
cat > ~/.radiant/radiant.conf << EOL
rpcuser=$(cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 12 | head -n 1)
rpcpassword=$(cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 32 | head -n 1)

listen=1
server=1
daemon=1

maxconnections=16

EOL
```
![](https://raw.githubusercontent.com/Antares-ES/Radiant-Guides/main/Compile-Node/img/08-UBUNTU-22_04_add-info-config-node.png)

--------------------------------------------------------------------------------------------

## **Useful commands**

- **START NODE:** `radiantd`
- **STOP NODE:** `radiant-cli stop`
- **NODE INFO:** `radiant-cli -getinfo`
- **NODE SYNCHRONISM:** `radiant-cli getblockchaininfo`
- **CONNECTIVITY:** `radiant-cli getnetworkinfo`
- **WALLET STATUS:** `radiant-cli getwalletinfo`
- **CREATE NEW ADDRESS:** `radiant-cli getnewaddress "Principal"`
- **GET PRIVATE ADDRESS KEY:**`radiant-cli dumpprivkey Direccion_Generada`
- **IMPORT PRIVATE KEY:** `radiant-cli importprivkey L1NLnbPQWaE3sDMedHgHy9q3wMjzZ2HCRAtgfQqfwXppAvnPBeit Test `
- **WALLET ADDRESSES:** `radiant-cli listreceivedbyaddress 1 true`

## **Mulai node**

![](https://raw.githubusercontent.com/Antares-ES/Radiant-Guides/main/Compile-Node/img/09-UBUNTU-22_04_start-node.png)