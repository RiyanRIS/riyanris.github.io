---
layout: post
title: Logging nodeJs via console.log
subtitle: aslinya sih pengen semua yang ditampilin di console.log tercatat kedalam file
tags: [nodejs, logging]
comments: true
---

Struktur kodingan seperti gambar dibawah

![Logging nodeJs via console.log](/uploads/20241009-alkshdiahw.png)

#### File logging.js
``` js
const fs = require("fs");
const path = require("path");

const ANSI_ESCAPE_REGEX =
  /[\u001b\u009b][[()#;?]*(?:[0-9]{1,4}(?:;[0-9]{0,4})*)?[0-9A-ORZcf-nqry=><]/g;

function getDateString() {
  const now = new Date();
  const year = now.getFullYear();
  const month = String(now.getMonth() + 1).padStart(2, "0");
  const day = String(now.getDate()).padStart(2, "0");
  return `${year}${month}${day}`;
}

function appendToLog(logMessage, logType = "log") {
  const logFileName = `${getDateString()}.${logType}`;
  const logDirectory = path.join(__dirname, "log");
  const logFilePath = path.join(logDirectory, logFileName);

  // Membuat direktori log jika belum ada
  fs.mkdirSync(logDirectory, { recursive: true });

  fs.appendFile(
    logFilePath,
    logMessage.replace(ANSI_ESCAPE_REGEX, "") + "\n",
    (err) => {
      if (err) {
        console.error("Gagal menulis log ke file:", err);
      }
    }
  );
}

const originalConsoleLog = console.log;
const originalConsoleError = console.error;

console.log = function () {
  const logMessage = Array.from(arguments).join(" ");
  originalConsoleLog.apply(console, arguments);
  appendToLog(logMessage, "log");
};

console.error = function () {
  const now = new Date();
  const hours = now.getHours();
  const minutes = now.getMinutes();
  const seconds = now.getSeconds();

  const timestamp = `[${hours}:${minutes}:${seconds}]`;

  const logMessage = timestamp + Array.from(arguments).join(" ");
  originalConsoleError.apply(console, arguments);
  appendToLog(logMessage, "error");
};

module.exports = console;
```

Load modulesnya di awal eksekusi file
``` js
const console = require("./logging.js")
```

selesai

nanti setiap console.* bakal tercatat di folder `./log/`

---

![contoh log](/uploads/20241009-kaushdkuagde.png)