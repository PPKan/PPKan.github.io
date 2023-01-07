---
layout: post
title: "如何自動匯出firestore資料(免費) -- 使用 firestore-export-import 及 windows"
subtitle: ""
date: 2023-01-17
author: "Peter"
header-img: "img/post-bg-2022.jpg"
tags: [程式, web dev]
---

這篇文章將講解如何進行firestore的備份。

[toc]

# 前言

firestore的備份不是一件很困難的事情，尤其是使用npm裡面的工具，在這邊我會介紹一些簡單的小方法進行備份，不會用到任何google提供的工具，例如bigQuery，主要以圖片為主介紹。

Google及網路上一般建議都是使用cronjobs及google bucket，那些方法確實不錯，但我覺得如果要花錢的話直接買一個aws ec2的server可能更方便一點，這邊是使用本機的蠢方法進行備份。

# 準備工具

1. Windows電腦
2. [node JS][1]
3. javscript的基本知識

# 方法

## 開資料夾，安裝 firestore-export-import
基本就是照著[firestore-export-import][2]提供的方法進行

```
mkdir firestore-backup
cd firestore-backup
npm install firestore-export-import
mkdir data //用來放備份檔案的資料夾
```

## 去 firestore 裡面下載需要用到的檔案

1. 進去設定
![](https://i.imgur.com/PTcGWYe.png)

2. 進去服務帳戶
![](https://i.imgur.com/Js4C4pR.png)

3. 下載私密金鑰
![](https://i.imgur.com/Ymtwnf2.png)

4. 把私密金鑰改名字，我是改成`serviceAccountKey.json` 然後放到你的資料夾裡面

## 設定程式

開一個新檔案，名子隨便，我是叫`app.ts`，內容我直接貼在下面，基本上我基於作者提供的範本做了幾個改動。

1. 不用options跟appName
2. 新增儲存檔案的方法

```typescript
const { initializeFirebaseApp } = require("firestore-export-import");

const serviceAccount = require("./serviceAccountKey.json");

const { backups } = require("firestore-export-import");
var fs = require("fs");
// If you want to pass settings for firestore, you can add to the options parameters
const options = {
  firestore: {
    host: "localhost:8080",
    ssl: false,
  },
};

// Initiate Firebase App
// appName is optional, you can omit it.
const appName = "[DEFAULT]";
// initializeFirebaseApp(serviceAccount, appName, options)
initializeFirebaseApp(serviceAccount);

// the appName & options are OPTIONAL
// you can initalize the app without them
// initializeFirebaseApp(serviceAccount)

backups() // Array of collection's name is OPTIONAL
  .then((collections) => {
    // You can do whatever you want with collections
    // console.log(JSON.stringify(collections))

    const jsonData = JSON.stringify(collections);
    const date = new Date();

    const year = date.getFullYear();
    const month = (date.getMonth() + 1).toString().padStart(2, "0");
    const day = date.getDate().toString().padStart(2, "0");
    const hours = date.getHours().toString().padStart(2, "0");
    const minutes = date.getMinutes().toString().padStart(2, "0");
    fs.writeFile(
      `data/firestore-backup-${year}${month}${day}-${hours}${minutes}.json`,
      jsonData,
      function (err) {
        if (err) {
          console.log(err);
        }
      }
    );
  });

```

## 設定windows自動化

我這邊為了求免費及方便用了windows的方法，如果有能力的同學可以用伺服器的cronjobs或是pm2之類的東西。

這邊的方法唯一的好處就是免費

1. win + R 打開執行
2. 輸入 `shell:startup`
3. 開新檔案，命名為`firestore-backup.cmd`(名稱隨便取就好)
4. 輸入我們要執行的內容
```
cd /d "D:\USER DATA\Desktop\firestore-backup"
node app.ts
```
cmd就是要輸入`/d`才能換磁碟，直接CD會過不去

這樣設定完之後，每次開機的時候就會自己存一份備份的檔案下來了。

要回復備份的話就不多說了，目前還沒用到。
## bonus
如果要每一段時間就備份一次的話請使用windows task scheduler，但注意這個不能拿來開機的時候用。

排程器這樣設定就可以，不知道自己的node在哪裡的同學可以在terminal輸入`where node`就知道了。

![](https://i.imgur.com/X2d64Ay.png)

[1]: https://nodejs.org/zh-tw/

[2]: https://www.npmjs.com/package/firestore-export-import