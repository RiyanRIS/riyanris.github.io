---
layout: post
title: Membuat User Baru Linux 
subtitle: ini yang biasa saya jalankan untuk membuat user baru linux
tags: [linux]
comments: true
---

![membuat user baru linux](/uploads/20241009-lasldinli.png)

---

### Buat usernya
``` bash
sudo adduser riyanris
```
- Biasanya sudah dibuatkan otomatis untuk home directory ke `/home/riyanris` namun jika belum terbuat, buat dulu pake user sudo `sudo mkdir /home/riyanris` kemudian setting user baru dengan perintah `sudo usermod -d /home/riyanris`
- Untuk [shellnya](https://askubuntu.com/a/141932) biasanya /bin/bash namun jika tersetting /bin/sh bisa dirubah pake perintah `sudo usermod -s /bin/bash` 

### Kasih group sudo ke user yang baru
``` bash
sudo usermod -aG sudo riyanris
```

### Cek usernya
``` bash
sudo cat /etc/passwd
```
- `sudo cat /etc/passwd | grep riyanris` gunakan ini untuk cari user tertentu saja

## Referensi
- https://linuxize.com/post/how-to-create-users-in-linux-using-the-useradd-command/
- https://linuxize.com/post/usermod-command-in-linux/
- https://askubuntu.com/questions/141928/what-is-the-difference-between-bin-sh-and-bin-bash