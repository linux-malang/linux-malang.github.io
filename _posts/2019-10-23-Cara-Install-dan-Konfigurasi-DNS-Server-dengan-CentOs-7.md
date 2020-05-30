---
title: "Cara Install DNS Server CentOs 7"
cover: "/assets/images/Konfigurasi-Forward-Zone.png"
date: 2019-10-23 11:43:00
author: Imron Rosyadi
layout: post
categories: blog
tags: dns-server domain centos
---

Pada kali ini saya akan membagikan ilmu dan pengetahuan mendasar seperti DNS Server. Terkait dengan DNS Server merupakan kebutuhan fundamental yang wajib dimiliki misalnya sebelum membuat suatu website. Sebelum kita mengenal lebih jauh dengan DNS kita harus tahu terlebih dahulu definisi dan tujuan dari DNS itu sendiri.
DNS merupakan kependekan dari Domain Name System yang merupakan sistem penamaan hirarkis dan desentralisasi untuk komputer, layanan, atau sumber daya lain yang terhubung ke internet atau jaringan pribadi. Ini mengaitkan berbagai informasi dengan nama domain yang ditetapkan, artinya menerjemahkan nama domain yang lebih mudah dihafal ke alamat IP Address yang diperlukan untuk mencari dan mengidentifikasi layanan komputer dan perangkat dengan protokol jaringan.
Tujuan dari DNS yaitu memudahkan identifikasi informasi website menggunakan nama domain, misalnya website A memiliki IP Address 1.1.1.1, website B menggunakan IP Address 2.2.2.2 dan website C menggunakan IP Address 3.3.3.3. Kita akan kesulitan untuk mengakses ketiga website tersebut dan akan lebih kesusahan lagi jika mengakses website dengan jumlah yang banyak menggunakan IP Address. Dengan demikian adanya DNS dapat memudahkan user dalam mengakses suatu website menggunakan nama domain. Lebih lengkapnya Anda dapat melihat tautan referensi berikut ini : https://en.wikipedia.org/wiki/Domain_Name_System

 Nah, yang melakukan konfigurasi dasar dari DNS itu sendiri adalah DNS Server. DNS Server adalah server yang melayani permintaan client untuk mengetahui alamat yang digunakan oleh sebuah website. Jadi misalnya Anda ingin mengakses website A, maka DNS Server yang akan mencari alamat dari website A agar client dapat mengakses ke website A. Untuk dapat mengakses sebuah website dengan nama domain perlu pengelolahan pada DNS itu sendiri (DNS Management), DNS Management ini memiliki record DNS yang dapat digunakan untuk mengarahkan domain terhadap suatu website secara lebih spesifik. 

 Selanjutnya kita akan mencoba melakukan instalasi dan konfigurasi DNS Server pada sistem operasi CentOs 7, kali ini saya menggunakan Virtual Private Server (VPS) supaya website saya juga langsung bisa diakses secara public. Adapun persiapan yang harus dilakukan sebelum melakukan instalasi DNS Server sebagai berikut : 

- **Sistem Operasi CentOS 7 Server**
- **Komputer Lokal/Server**
- **Domain Jika Diperlukan**

**A. Instalasi DNS Server**

1. Setelah semua kebutuhan sudah terpenuhi install terlebih dahulu paket DNS pada CentOs 7-server Anda, paket DNS yang digunakan pada CentOs adalah bind. Untuk melakukan instalasi paket tersebut, pastikan Anda sudah melakukan update paket terlebih dahulu dengan perintah : 

```
yum update -y 
```

2. Tunggu beberapa saat dan pastikan update paket telah selesai 100%, kecepatan proses update tergantung dari koneksi internet yang Anda gunakan. Selanjutnya install paket bind pada CentOs 7-server dengan perintah : 

``` 
yum install bind bind-utils -y
```

**B. Konfigurasi DNS Server**

1. Konfigurasi pertama DNS Server yaitu pada file named, backup terlebih dahulu untuk menghindari kegagalan service yang berjalan  pada DNS Server sehingga kita bisa mengembalikan konfigurasi secara default. Untuk melakukan konfigurasi DNS Server Anda dapat menggunakan teks editor favorit Anda, contohnya disini saya menggunakan `vim`. 

```
vim /etc/named.conf
```
2. Sesuaikan dengan konfigurasi IP Address yang Anda gunakan saat ini, untuk detail penggunaannya dapat melalui gambar 1 dibawah ini : 

![Screenshot DNS-server](/assets/images/Konfigurasi-IP-Address-named.png)

3. Selanjutnya Anda perlu mendeskripsikan nama domain yang akan digunakan pada website Anda, sehingga Anda perlu menambahkan baris konfigurasi seperti berikut : 

```
zone    "domain.tld"  {
        type master;
        file    "/etc/named/domain.tld.zone";
 };

```

> Keterangan : 
> `domain.tld` merupakan root domain yang akan digunakan untuk manajemen DNS, isikan dengan domain yang ingin Anda gunakan. Pada > baris file tersebut merupakan peletakan nama file yang akan disimpan, sehingga Anda harus membuat file yang sesuai dengan 
> deklarasi yang Anda buat pada nama file tersebut. 

4. Simpan konfigurasi tersebut. 

5. Buatlah nama file `domain.tld.zone` untuk forward zone sesuai dengan nama file yang dideklrasikan sebelumnya yang terletak pada direktori `/etc/named/`. Adapun beris konfigurasinya dapat menggunakan dengan salah satu contoh pada gambar 2 berikut ini : 

![Screenshot DNS-server](/assets/images/Konfigurasi-Forward-Zone.png)


> Keterangan : 
> Sesuaikan dengan nama domain dan IP Address yang Anda gunakan. 

6. Simpan konfigurasi dan coba lakukan uji coba terhadap konfigurasi DNS yang telah dilakukan. 

Anda dapat melakukan uji coba dari hasil konfigurasi DNS Anda menggunakan `nslookup` atau `dig`. Misalnya disini saya menggunakan perintah `dig`, Anda dapat mengikuti langkah-langkah berikut ini : 


```
# whois domain.tld | grep Server
Name Server:NS1.DOMAIN.TLD
Name Server:NS2.DOMAIN.TLD

# dig domain.tld +short 
IP-Addr-DNS-Server

# dig @ns1.domain.tld domain.tld +short
IP-Addr-DNS-Server

# dig @ns2.domain.tld domain.tld +short
IP-Addr-DNS-Server
```

Jika hasil pengetesan konfigurasi DNS server sudah sesuai dengan yang dikonfigurasi sebelumnya, maka akan menampilkan IP Address yang digunakan pada domain tersebut. Dari hasil pengetesan diatas dapat dilihat bahwa domain sudah resolv ke IP Address yang digunakan. Sebagai informasi tambahan apabila Anda melakukan konfigurasi DNS menggunakan VPS maka Anda perlu menunggu waktu propagasi dan waktu propagasi paling lambat 2x24 jam tergantung dari resolver ISP yang Anda gunakan, namun jika Anda menggunakan VM local Anda hanya menunggu beberapa saat domain tersebut akan resolv atau biasanya dapat resolv secara langsung tergantung dari segi konfigurasi resolver juga (resolv.conf). 

Untuk memastikan domain Anda sudah bisa diakses, instal web server (misalnya : apache) pada CentOs 7-server Anda dengan perintah : 

```
yum install httpd
```

Coba akses root domain Anda pada web browser, apabila berhasil maka akan tampil seperti pada gambar 3 berikut ini : 

![Screenshot DNS-server](/assets/images/Hasil-web-server.png)

Demikian informasi yang dapat saya bagikan semoga ilmu dan pengetahun tentang DNS Server ini dapat bermanfaat dan barokah buat kita semua. Aamiin 

**Sumber Referensi**:
- [**Definisi DNS**](https://en.wikipedia.org/wiki/Domain_Name_System)