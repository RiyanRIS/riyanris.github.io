---
layout: post
title: Otomatis hapus log lama di linux
subtitle: 
tags: [logging, linux]
comments: true
---

- masuk server via ssh, sesuaikan direktorinya, saya pake _/home/riyanris/cron/_
- bikin file **.sh** baru `sudo nano clean_log.sh`
- isinya kurang lebih seperti ini

  ``` sh
  #!/bin/bash

  # Direktori log
  LOG_DIR="/var/www/html/res/tmp"

  # Menghapus log yang lebih tua dari 30 hari
  find "$LOG_DIR" -type f -name '*.log' -mtime +30 -exec rm {} \;
  ```
- kode diatas menghapus file yang berusia lebih dari **30 hari** di direktori _/var/www/html/res/tmp_
- jagnan lupa sesuaikan path lognya dan jangka waktu filenya lalu simpan
- buat cron job `sudo crontab -e`
- tambah line kurang lebih gini `0 2 * * * /home/riyanris/cron/clean_log.sh`

  ![alt text](/uploads/20241009-akusgduagdwu.png)

- sesuaikan jam pengecekan, disitu tak setting setiap pukul 2 pagi, kalo bingung gunakan [crontab.guru](https://crontab.guru/)
- simpan, selesai