---
layout: post
title: "Mengupload Project Website Laravel Ke Shared Hosting Dengan Menggunakan Git-FTP"
date: 2019-10-19 23:17:08
author: Nur Mukhammad Agus
categories: blog
tags: git-ftp laravel hosting
---

### **Mengupload Project Website Laravel Ke Shared Hosting Dengan Menggunakan Git-FTP**

<p align="center">
  <img width="710" height="429" src="https://cdn.s3-id-jkt-1.kilatstorage.id/001%20-%20Git-FTP.png">
</p>

Sebelum kita mengupload project website Laravel yang kita miliki ke Shared Hosting, terlebih dahulu kita harus sudah melakukan proses deploy framework Laravel pada komputer lokal maupun server yang kita miliki dan berikut kebutuhan yang perlu kita siapkan sebelumnya, antara lain:

- **Internet**
- **Komputer Lokal/Server**
- [**Framework Laravel**](https://laravel.com/docs/5.8/installation)
- [**Git**](https://git-scm.com/downloads)
- [**Git-FTP**](https://git-ftp.github.io/)
- **Shared Hosting**
- **Domain**

Setelah semua kebutuhan diatas sudah siap, maka proses selanjutkan kita masuk ke bagian dimana kita mengupload project website Laravel ke Shared Hosting, pastikan sebelum kita mengupload project website yang kita miliki, kita harus sudah menambahkan domain pada Shared Hosting yang kita gunakan.

Sebelumnya kita juga perlu membuat akun FTP pada Shared Hosting yang kita miliki, nantinya data credential akun FTP yang telah kita buat dibutuhkan untuk akses ke FTP Server pada Shared Hosting yang kita miliki untuk keperluan proses transfer data dan berikut langkah-langkahnya:

##### **Menginstall Git & Git-FTP Pada Komputer Lokal/Server**

```
yum update -y && yum install -y git (CentOS) atau apt-get update -y && apt-get install -y git (Ubuntu/Debian)
cd /opt/
git clone https://github.com/git-ftp/git-ftp.git
cd /git-ftp/
cp git-ftp /usr/bin/git-ftp
```
##### **Mengupload Project Website Laravel Ke Shared Hosting**
```
cd /path/laravel/
git init
git add .
git commit -m "Mengupload Project Website Laravel Ke Shared Hosting"
git config git-ftp.url "ftp://domain.tld:21"
git config git-ftp.password "ftppassword"
git config git-ftp.user "ftpuser"
git config git-ftp.syncroot /path/laravel/
git config git-ftp.insecure 0
git-ftp init atau git-ftp init --syncroot /path/laravel/
```
##### **Mengupload File Baru Ke Dalam Project Website Laravel**
```
cd /path/laravel/
touch file.txt
echo "File Baru Project Website Laravel" > file.txt
git add file.txt
git commit file.txt -m "Mengupload File Baru Ke Dalam Project Website Laravel"
git-ftp push atau git-ftp push --syncroot /path/laravel/
```
##### **Menghapus File Didalam Project Website Laravel**
```
cd /path/laravel/
git rm file.txt
git commit -m "Menghapus File Didalam Project Website Laravel"
git-ftp push atau git-ftp push --syncroot /path/laravel/
```
##### **Mendownload Project Website Laravel Pada Shared Hosting Ke Lokal/Server**
```
cd /path/laravel/
git init
git commit -m "Mendownload Project Website Laravel Pada Shared Hosting Ke Lokal"
git-ftp pull -u ftpuser -p ftppassword ftp://domain.tld:21 atau git-ftp download -u ftpuser -p ftppassword ftp://domain.tld:21
```

Setelah semua proses diatas selesai dan berhasil dilakukan, maka project website Laravel yang saat ini kita kelola pada Shared Hosting sudah dapat dipublish.

Demikian sedikit pengetahuan dan pengalaman yang dapat saya bagikan, semoga apa yang telah saya sampaikan dapat bermanfaat bagi kita semua.

---

**Sumber Referensi**:
- [**Cara Upload File ke Server FTP ala Git**](https://www.petanikode.com/git-ftp/)
- [**Mudah Mengupload Proyek berbasis Git ke Server Hosting Menggunakan Git-FTP**](https://www.codepolitan.com/upload-deploymet-proyek-berbasis-git-ke-server-shared-hosting-menggunakan-git-ftp)
