---
layout: post
title: "OSCP第一次考試就不及格"
subtitle: "分享一些考試中學到的知識"
date: 2024-01-03
author: "Peter"
header-img: "img/post-bg-2023.jpg"
tags: [資安, oscp]
---

> 那些殺不死我的，必然使我更強大 -- 尼采

關於之前的心路歷程，請參照 [OSCP 課程心得 part1 -- Challenge lab開始前的紀錄 - PPKan PBlog](https://peterkan.tw/2023/06/20/oscp-part-one-review/)

## 前言

是的，OSCP沒過。

雖然是這樣講，一則以喜，一則以憂，雖然是蠻難過的，但確實是蠻有收穫，這邊想要分享一下考試前的準備跟心路歷程，還有我之後的規劃等等一併整理在這篇。有鑑於上一篇文章只停留在lab開始前的時間，這篇一併也會把lab的90天合併進去說明。

我希望這篇文章同時有著可以讓正在準備或是打算要考OSCP的同學的前車之鑑，也可以讓我自己重新整理一下心境好好準備應付接下來的第二次考試。



## Challenge Labs

Challenge Labs 是 oscp 課程的其中一個環節，除了課程的課本、習題之外，還會有額外的6個lab讓學生去練習課堂上所學習到的知識。lab的內容設計還蠻好的，給了3個很大的AD Domain去練，然後還有3個考試模擬環境。

lab本身的難度都蠻剛好的，很多大大小小的地方會串在一起，基本上用課堂上學到的知識都能過，最重要的是可以大量的練習如何去做跳板(pivot)。跳板這個概念會很重要，知道網路怎麼透過socks連接，以及一些工具例如[Chisel](https://github.com/jpillora/chisel)、[Ligolo-ng](https://github.com/nicocha30/ligolo-ng)都會對工具的熟練度有很好的提升。

在6個lab中我完成了其中5個，有一個因為時間不夠的關係先捨棄掉了，學到了不少東西，而後來想想可以做得更好的地方有：

1. 把6個lab都做完，就算是簡單地看過去也好
2. 在其中幾個像考試的lab的環境裡，試著把自己逼緊一點，去模擬考試的環境
3. 做筆記

第一點是因為到後來會發現儘管考試的主題都是從課本裡面出，但變化真的蠻大的，需要去適應同樣一個漏洞會以什麼樣不同的模式呈現出來。如果時間不夠的情況下，至少先知道有什麼樣的變化手法，筆記下來，考試的時候就可以多一份底氣。

第二點要講到我認為考試最困難的地方 -- 緊張感，後面會講得比較細一點，在練習的時候要試著把自己處在那個24小時的緊張環境下去練習應付每一個挑戰，練習跟自己的心態對抗。

再者是做筆記，筆記這件事情我想會做滲透測試的同學們多多少少都蠻熟稔的，講是這樣講，怎麼做一份好的筆記真的是很不容易。我是考試考到一半才驚覺自己應該要好好做筆記。我認為做筆記的要點在於說，**不是把「成功」的事情記錄下來，而是把「做過的」事情一一筆記下來**才不會一直重蹈之前的覆轍，就算事後知道問題出在哪裡也可以比較好的去擴充自己的知識庫。到後來我是習慣用每個port做過的事情去記錄下來，這真的很重要。

關於做筆記的部分，ippsec在[bashed](https://youtu.be/2DqdPcbYcy8?si=aIs4_PVMwI9r7jeR)這部影片有示範，其次是offsec的s1ren在[vault](https://youtu.be/JocbrhLXuss?si=TqksXWxBqX6-sFTQ)的教學影片中也有示範怎麼去紀錄，我覺得可以先參考一下，而具體要怎麼做筆記還是得依自己的習慣去走。我建議可以用notion去做，一方面是好用，另一方面是搜尋功能很方便。如果不想連網的話可以用joplin，個人覺得比cherrytree好用。

關於 challenge labs 本體沒有什麼特別值得一筆的點，如果遇到問題的話可以去offsec的discord頻道上面查，幾乎都會有，就算問問題他們也會很友善地回應你。



## 考前準備

考試之前我遵循了reddit上面很多人的做法，除了做offsec本身的lab之外還有自己再去獨立練習了[TJ Null的考試清單](https://docs.google.com/spreadsheets/u/1/d/1dwSMIAPIam0PuRBkCiDI88pU3yzrqqHkDtBngUHNCw8/htmlview#)。這份清單基本上是TJ Null這位前輩覺得OSCP可以用哪些靶機去做練習的一份整理，坊間也是提到oscp都會建議用這份清單去做練習。

清單上面除了offsec本家的proving grounds之外，也有hackthebox等等，我自己是只練習了Proving Grounds的部分，而HackTheBox的部分只有在OSCP之前有摸一下，後來都沒碰了。這點是我覺得最可惜的。倒不是說PG Practice/Play不好，而是HackTheBox還是有它不可取代的地方就是由ippsec提供的影片的walkthrough。

像開頭文章裡就有說的，ippsec可以提供一個很好的全方位的講解，並且很清楚地可以知道自己與高手之間的差異，在自己已經準備得差不多的時候回頭去對照一下應該也會挺不錯的。

以時程表來說的話是：HTB(IPPSEC) > OSCP課程 > OSCP labs > PG > HTB(IPPSEC) 這樣的流程我自己覺得會是最佳解。這也是我這次會著重的部分。



## 考試時的重點 - 筆記及心態

關於考試順序，我也是照一般的推薦，先從AD set開始，拿到之後再去解剩下的20分，比較可惜的是我在AD set卡了十幾個小時遲遲沒辦法拿到進展，也導致後續時間沒辦法成功進行，拿到剩下的60分以完成考試。最後考試以30分結束。

經過這次考試我發覺最重要的還是

1. 筆記
2. 心態

坊間有很多很多的心得文，裡面都會講到他們覺得怎樣才是最好，事實上我也看了不少，但比較可惜的是那些心得文沒有實際體驗過真正的考試環境的話對我來說很難理解，理解到之後才發現原來大家講的都是真的。

關於筆記上面有寫了不少，做筆記很重要。而心態也很重要，適當地放鬆，適當地休息，遇到問題之後把問題寫下來，把筆記規劃好，有一個完整的流程圖讓自己去照流程去進行是最重要的。我也建議可以做做看自己的流程圖，嘗試照自己的規劃進行每一次的滲透測試。

至於考試沒過嘛，其實reddit上面很多人也沒過，倒不是什麼大事情，就是自己要多練習，準備好面對挑戰的心態，不要被打倒啦！

## 後續規劃

現在學校放寒假了(歡呼)，終於有辦法好好的開始準備第二次考試，第一次考試的時候被學校報告各種夾擊，在沒辦法的情況下只好安排了其中一個日期，也不知道那個日期到底有沒有辦法好好考試，這邊還是要提醒一下，在OSCP的lab結束後的120日內要安排考試，我當初以為是有一年時間就打算拖到寒假，結果時間緊迫安排在學期中間把自己弄得蠟燭兩頭燒實在是不應該。

而後續的考試日期也已經確定於二月初，關於從今天開始到二月初的準備過程我應該會像上面給大家的心得一樣，試著把筆記做好，並去看一些自己不太擅長的領域，尤其是跟ippsec一起學習，運氣好的話，第二次應該會沒什麼問題。我們到時候再見。

--- 

感謝閱讀，本篇文章同步發表於FB粉絲專頁[PP學習筆記](https://www.facebook.com/pplearningnote)