---
layout: post
title: "Lsyncd Untuk Sinkronisasi File dan Direktori Pada Server CentOS 7"
date: 2019-10-08 00:00:00
author: Nur Hamim
categories: blog
tags: lsyncd rsync linux centos 
---

### PENDAHULUAN

Lsyncd singkatan dari "Live Syncing Daemon", sebuah service yang digunakan untuk menyinkronkan atau mereplikasi file dan direktori secara lokal dan jarak jauh setelah interval waktu tertentu menggunakan rsync dan ssh di backend untuk authentication.

Apa bedanya dengan rsync?

Apabila Anda menggunakan rsync Anda perlu "job" sebagai perintah untuk sinkronisasi, dengan lsyncd Anda tidak perlu membuat "cron job" untuk menjalankan sinkronisasi.

Lsyncd bekerja pada arsitektur Master dan Slave di mana ia memantau direktori pada server master, jika ada perubahan atau modifikasi yang dilakukan maka lsyncd akan mereplikasi yang sama pada server slave-nya setelah interval waktu tertentu.

Berikut topologi yang akan digunakan pada panduan ini:

![Topologi](/assets/images/topologi-lsyncd.jpg)

**Kebutuhan:** 

- OS: CentOS 7
- Srv-prod   - IP: 10.10.1.11
- Srv-Backup - IP: 10.10.1.7  
- Direktori yang akan di sinkronasi "/var/www/html"

**LANGKAH KERJA**

**Langkah 1**

Silakan login pada "srv-prod" dan setup ssh key authentication 

```
[root@srv-prod ~]# ssh-keygen -t rsa
```

Copy publik key ke server tujuan "srv-backup"

```
[root@srv-prod ~]# ssh-keygen -t rsa
```
Untuk memastikan silakan akses ssh dari "srv-prod" ke "srv-backup" 

```
[root@srv-prod ~]# ssh root@10.10.1.7
```

**Langkah 2**

Instalasi lsyncd pada CentOS 7 menggunakan perintah berikut

```
[root@srv-prod ~]# yum install epel-release 
[root@srv-prod ~]# yum install lsyncd -y
```
Buka file **/etc/lsyncd.conf** untuk melakukan konfigurasi

```
----
-- User configuration file for lsyncd.
--
-- Simple example for default rsync, but executing moves through on the target.
--
-- For more examples, see /usr/share/doc/lsyncd*/examples/
--
-- sync{default.rsyncssh, source="/var/www/html", host="localhost", targetdir="/tmp/htmlcopy/"}

settings {
        logfile         = "/var/log/lsyncd/lsyncd.log",
        statusFile      = "/tmp/lsyncd.stat",
        statusInterval  = 1,
}

sync {
        default.rsync,
        source  = "/var/www/html",
        target  = "10.10.1.7:/var/www/html",
}

rsync = {
        update  = true,
        perms   = true,
        owner   = true,
        group   = true,
        rsh     = "/usr/bin/ssh -l root -i /root/.ssh/id_rsa"
}
```

Silakan sesuaikan "source" yang akan di backup dan "target" server backup nya. Untuk konfigurasi di atas dapat Anda sesuaikan dengan kebutuhan dan Anda dapat explore nya melalui link berikut: [**The Configuration File**](https://axkibe.github.io/lsyncd/manual/config/file/)

Selanjutnya silakan start dan enable service lsyncd, pastikan statusnya running sebagai berikut:

```
[root@srv-prod ~]#
[root@srv-prod ~]# systemctl enable lsyncd
[root@srv-prod ~]# systemctl start lsyncd
[root@srv-prod ~]# systemctl status lsyncd
● lsyncd.service - Live Syncing (Mirror) Daemon
   Loaded: loaded (/usr/lib/systemd/system/lsyncd.service; enabled; vendor preset: disabled)
   Active: active (running) since Sat 2019-10-26 16:37:47 UTC; 26s ago
 Main PID: 9457 (lsyncd)
   CGroup: /system.slice/lsyncd.service
           └─9457 /usr/bin/lsyncd -nodaemon /etc/lsyncd.conf

Oct 26 16:37:47 srv-prod systemd[1]: Started Live Syncing (Mirror) Daemon.
[root@srv-prod ~]#
```
**Langkah 3**

Memastikan lsyncd telah berjalan dengan sempurna, silakan membuat file atau direktori pada "/var/www/html" dan pastikan di sisi server target "srv-backup" telah ada file tersebut di direktori "/var/www/html"

```
[root@srv-prod ~]#
[root@srv-prod ~]# cd /var/www/html/
[root@srv-prod html]#
[root@srv-prod html]# touch klim.txt
[root@srv-prod html]# mkdir klim-mantap
[root@srv-prod html]# dd if=/dev/zero of=piye-rek.html count=1024 bs=1024
1024+0 records in
1024+0 records out
1048576 bytes (1.0 MB) copied, 0.00219632 s, 477 MB/s
[root@srv-prod html]#
[root@srv-prod html]# ls
klim-mantap  klim.txt  piye-rek.html
[root@srv-prod html]#
```
![Create File](/assets/images/srv-prod.png)

Akses server backup dan pastikan file telah ada seperti berikut

```
[root@srv-backup ~]#
[root@srv-backup ~]# cd /var/www/html/
[root@srv-backup html]# ls
klim-mantap  klim.txt  piye-rek.html
[root@srv-backup html]#
```
![Hasil Backup File](/assets/images/srv-backup.png)

Memberikan permission pada file "piye-rek.html" menjadi 755 

```
[root@srv-prod html]#
[root@srv-prod html]# chmod 755 piye-rek.html
[root@srv-prod html]# ls -lah
total 1.0M
drwxr-xr-x. 3 root root   62 Oct 26 16:41 .
drwxr-xr-x. 4 root root   33 Oct 26 09:49 ..
drwxr-xr-x  2 root root    6 Oct 26 16:41 klim-mantap
-rw-r--r--  1 root root    0 Oct 26 16:41 klim.txt
-rwxr-xr-x  1 root root 1.0M Oct 26 16:41 piye-rek.html
[root@srv-prod html]#
```

![Set Permission File](/assets/images/srv-prod-set.png)

Pastikan di server backup juga berubah menyesuaikan server prod seperti berikut hasilnya

[root@srv-backup html]#
[root@srv-backup html]# ls -lah
total 0
drwxr-xr-x. 3 root root 62 Oct 26 16:51 .
drwxr-xr-x. 4 root root 33 Oct 26 10:10 ..
drwxr-xr-x  2 root root  6 Oct 26 16:41 klim-mantap
-rw-r--r--  1 root root  0 Oct 26 16:41 klim.txt
-rwxr-xr-x  1 root root  0 Oct 26 16:51 piye-rek.html
[root@srv-backup html]#

![Hasil Perubahan File](/assets/images/srv-backup-set.png)

Selanjtunya melihat log pengiriman atau perubahan yang telah di lakukan di sisi srv-prod sebagai berikut

```
[root@srv-prod html]#
[root@srv-prod html]# tail -f 10 /var/log/lsyncd/lsyncd.log
tail: cannot open '10' for reading: No such file or directory
==> /var/log/lsyncd/lsyncd.log <==
/klim-mantap/
Sat Oct 26 16:54:51 2019 Normal: Finished a list after exitcode: 0
Sat Oct 26 16:55:14 2019 Normal: Calling rsync with filter-list of new/modified files/dirs
/piye-rek.html
/
Sat Oct 26 16:55:14 2019 Normal: Finished a list after exitcode: 0
Sat Oct 26 16:55:45 2019 Normal: Calling rsync with filter-list of new/modified files/dirs
/piye-rek.html
/
Sat Oct 26 16:55:45 2019 Normal: Finished a list after exitcode: 0
```
Saat ini lsyncd telah berjalan dengan normal. 

Sekian panduan terkait sinkronisasi file dan direktori pada server CentOS 7. 

---

**Referensi:**

- [**Lsyncd - Live Syncing (Mirror) Daemon**](https://axkibe.github.io/lsyncd/)
