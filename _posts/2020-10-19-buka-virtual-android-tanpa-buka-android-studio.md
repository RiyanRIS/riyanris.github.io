---
layout: post
title: Buka Virtual Android Tanpa Buka Android Studio
gh-badge: [star, fork, follow]
tags: [android]
comments: true
---

pertama pakai kode

```cd ~/Android/Sdk/tools && ./emulator -list-avds```

kalau ga bisa pake ini

```cd ~/Android/Sdk/tools/bin && ./avdmanager list avd```

maka hasilnya kurang lebih gini

![](https://i.ibb.co/b5PF7rx/image.png)

lalu perintah dibawah buat buka

```cd ~/Android/Sdk/tools && ./emulator -avd NAME_OF_YOUR_DEVICE```

jadinya gini

![](https://i.ibb.co/2ccNmJZ/image.png)

sumber: https://stackoverflow.com/a/42845150

[https://stackoverflow.com/a/42845150](https://stackoverflow.com/a/42845150 "https://stackoverflow.com/a/42845150")
