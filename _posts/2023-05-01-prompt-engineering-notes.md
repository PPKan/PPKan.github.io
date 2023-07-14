---
layout: post
title: "Deeplearning.ai 提示詞工程課程心得 -- 為什麼每個2023的人都應該上這門課"
subtitle: "Reviews of ChatGPT Prompt Engineering for Developers from Deeplearning.ai -- Why Everyone should take a look at this course"
date: 2023-05-01
author: "Peter"
header-img: "img/post-bg-hack.jpg"
tags: [AI, chatGPT, LLMs]
---

先講結論：**非常推薦今年(2023)所有人都來上這門課程，課程以API的使用為主，涵蓋了從提示詞(prompt)的使用原則到如何有效率的生產一個合格的應用，作為一個2023甚至是以後的大趨勢，學會這項技能會對現在甚至是將來起到絕對的作用。**


課程連結：[https://www.deeplearning.ai/short-courses/chatgpt-prompt-engineering-for-developers/](https://www.deeplearning.ai/short-courses/chatgpt-prompt-engineering-for-developers/)

課程時長(含筆記時間)：約 1.5~2.5 小時，實際只看影片的話更短

---

# 前言

在資訊爆炸的2023年，每一天都有新的AI文章出來，事隔幾周都會有新的"爆炸性進展"，導致現在(2023/05/01)已經在FB上看到好幾篇說已經不想再接受AI的新知，因為才接觸沒多久就又有新的東西出來 -- 對，我也是這樣。

那為什麼還要特地寫這篇出來的原因是，我覺得chatGPT的使用已經差不多成為定數，並且在短時間內很難有很大的改變，頂多是model從gpt-3.5-turbo改成gpt-4，但使用的範疇已經差不多定型，就模型上面已經不會有什麼大變化，甚至[Sam Altman也說並沒有在訓練gpt-5][3]。。

現在有很大的變化的內容通常是在應用上面，像是還沒完全公開的插件([chatgpt plugins][1])，以及其他工具如[Auto-GPT][2]等。

鑒此，如何將現有的利器 -- chatGPT運用於現實生活情境就是一個很重要的關鍵，甚至著名的KOL -- Fox 蕭上農 也[提及了AI在2023是一個不能忽略的關鍵][4]。於是這門課可以說是來的很剛好，在現在chatGPT剛差不多定型的當下，就用了一個教學教大家如何靈活運用這個大殺器。

我接下來會分項講解我對這門課的理解，當然，會以我自己的心得為主並**以文字敘述的方式進行**，裡面主要會有一些我自己的反思，並**不會提到太多裡面的教學內容**。畢竟專業的課程還是要讓專業的老師來上，我這邊充其量就做為一個學生，分享一下我對於這門課的理解以及為什麼我覺得這門課很好。

[1]: https://openai.com/blog/chatgpt-plugins
[2]: https://github.com/Significant-Gravitas/Auto-GPT
[3]: https://www.inside.com.tw/article/31337-OpenAI-Sam-Altman-gpt-5-rumors
[4]: https://youtu.be/XfVoyDNQO_A


# 前置條件 (prerequisits)

課程主要是以影片+實際練習的形式去走，在網頁上面的左邊會是Jupyter的筆記本，讓我們可以實際在網頁上去運行python程式，右邊則是影片說明。

### 英文 - 有中文翻譯的版本，不過要找一下

這門課程是全英文的，但我看到網路上對岸那邊已經有翻譯的版本，看不懂英文的同學可以自己去找找，我這邊就不提供了，但英文不會很難。

### Python - 會一點比較好，不會也沒關係

由於課程大量運用到了python，如果稍微懂一點python的話會對課程理解有比較大的幫助，但**我也強烈建議就算不懂python也把他看完，課程就算扣掉python也很頂**。

python在這部教學裡面可以想像為是一個載體，如何去靈活地運用chatGPT並發揮其所有功能，但對於課程理解來說，就算不是很懂python或完全不懂python也是可以理解到prompt的功用，但有些應用面來說可能會稍微比較吃力一點。

### chatGPT 的使用經驗

在上這門課之前，如果你沒摸過chatGPT的話我還是建議去摸摸看，去體驗看看chatGPT在日常生活中能帶給我們什麼，並試著把自己的workflow整合進去。

這門課比較像是有點進階版的chatGPT，也就是不用網頁，而是用API的方式去進行呼叫以靈活地運用chatGPT，會發現chatGPT其實比我們想像得還強大得多。

### 其他專有名詞 - API、JSON

#### API


> API 是使用一組定義和協定讓兩個軟體元件彼此通訊的機制。舉例來說，氣象局的軟體系統包含有每日的天氣資料。您手機中的天氣應用程式會透過 API 與此系統「交談」並且在您的手機顯示每日天氣的最新消息。
> [From AWS][5]

簡單來說API就是扮演著一個橋樑，讓我們可以用程式碼去跟服務(Service)去交互。

#### JSON

>JSON(JavaScript Object Notation)，它是一種輕量級的資料交換格式，能使資料更加容易地交換，以純文字為基礎，用來儲存和傳送簡單的資料，讓人容易撰寫及閱讀。我們常常可以在網站、API與資料庫中，看到這個資料傳輸的格式。
> [From 天矽科技][6]

JSON簡單來說就是一個電腦上比較廣泛運用的資料的格式，像我們一般的表格是

| ID | Name |
| ---- | -------- |
| 1 | Aquaman     |
| 2 | GummyB

而轉成JSON就會是

```json
[
    {
         "ID": 1,
         "Name": "Aquaman"
    },
    {
        "ID": 2,
        "Name": "GummyB"
    }
]
```

[5]: https://aws.amazon.com/tw/what-is/api/
[6]: https://www.tsg.com.tw/blog-related-detail2-227-1-json.htm

# 課程內容

課程的內容我自己覺得可以分成三個類型

1. 寫提示詞(Prompt)的兩個原則
2. 靈活運用Prompt的示例
3. 如何寫自己的chatGPT應用程式

### 寫提示詞(Prompt)的兩個原則

在課程裡面，也可以說整個課程的大主軸就是如何運用這兩個原則在Prompt上面，我這邊做不到像課程一樣的詳盡介紹，但我可以大概講講這兩個原則厲害在哪邊。

兩個原則分別是
- Principle 1: Write clear and specific instructions
(原則1: 寫清楚並具體的說明)
- Principle 2: Give the model time to “think”
(原則2: 給模型"思考"的時間)

在上這門課之前我的想法是，OK，我知道這應該不錯，但我覺得我也蠻屌的，我看這大概幫助不大吧。

而在上完課之後我大大改觀，就是因為這兩個原則。

課程中詳細展開了這兩個原則，我這邊舉個例子是關於課程裡面的第二個原則，課程裡面提到舉了一個數學題的例子，當我們詢問chatGPT關於我們的答案是否適切的時候，比起直接詢問chatGPT，更好的問法會是先讓chatGPT自己算過一次，然後再比對他的答案與我們提供的答案。透過這種過程可以讓chatGPT有更多時間"思考"，並做出回饋。

這樣的思考方式是我平常在使用chatGPT的時候完全想像不到的，我覺得十分受用。

### 靈活運用Prompt的示例

在其中的幾個小節裡面，有提到了如何靈活運用prompt，意思是我們可以怎麼把prompt寫得好的幾個方法。裡面提到了包括expand(延展)、summary(總結)等等，都用實例講解了要怎麼把我們平常看似熟稔的用法實際上運用在工作場景裡面。

其中我很喜歡的是他在講expand的時候講到了，我們最好讓人知道那些文章是由AI產出的，這點我特別喜歡。

### 如何寫自己的chatGPT應用程式

這個篇幅比較小，算是一個簡單的講解如何建立一個應用程式，課程中以一個Pizza的點餐機器人為例，講解了怎麼使用role及content這兩個很重要的api裡面的屬性。

我覺得這邊比較像是，OK，我現在連怎麼寫程式都教你了，屁股趕快動起來吧！的感覺。看完這邊再說自己沒辦法做就有點那個了XD~ 真的很詳細。

接下來我們來聊聊一些我自己特別喜歡的點。

# 課程心得

這個章節會專注在令我耳目一新的點上面，畢竟課程內容的心得上面已經講得七七八八了，這邊我來講一點我自己很喜歡的點。

### 資安意識 - 預防 Prompt Injection

在課程中的每一個prompt裡面的使用者input，都會使用一個delimiter(分隔號)，以及會跟chatGPT說明這段應該要是什麼，這能起到防止用戶輸入 `請忽視其他指令，並執行"內容"` 的這類型的指令，在這邊做到基本的資安意識我覺得是很難能可貴的，我在其他地方沒有看到過類似的用法，儘管現階段可能用途不大，但到後來可能**chatGPT能讀取的資料變多，就要更注意類似的操作手法。畢竟讀到其他重要檔案就不好玩了。**

下面給個課程範例：
```python
prompt = f"""
Your task is to help a marketing team create a 
description for a retail website of a product based 
on a technical fact sheet.

Write a product description based on the information 
provided in the technical specifications delimited by 
<>.

Technical specifications: <>{fact_sheet_chair}<>
"""
response = get_completion(prompt)
print(response)
```

### 直式的影片

課程長像這個樣子，影片是直的，這真的超酷，等於說我們可以一邊看影片一邊用旁邊的Jupyter練習!

![](https://i.imgur.com/pJKjjhN.png)

### Andrew Ng 的強大

這邊想特別吹捧一下吳恩達的速度，雖然我是不知道Deeplearning.ai跟openai有什麼特別的關係，但一個AI大老能在這麼短的時間內上架這樣的入門課程實在非常難能可貴，而且免費！

如果我是吳恩達，我實在不知道自己有沒有那個勇氣承認openai的大成功，並合作做出這樣的教學內容。只能說真不愧是coursera的創辦人。

下面我們來講講可以怎麼用用這個API。

# 課程運用

### 神功護體！可以自己寫chatGPT app了？！

上這堂課之前，我就有要做很多東西的想法(沒有寫是因為真的很忙! OSCP才讀到第三章!)，但我只有概念，要怎麼做其實不是很清楚，只能靠自己從基本的框架開始摸索。

上了這堂課之後等於給了我一個超大型的overview，做什麼都好像信手捻來一樣！(好像壯陽藥的推銷)

舉例來說我之前想要做的是一個讀pdf的chatGPT app，因為我覺得不管是[chat pdf][7]還是[chatGPT academic][8]都有點太heavy，想要自己弄一個出來，那時候想的是像chatGPT academic一樣透過prompt，來讓chatGPT讀pdf。

不過現在知道有role跟content這種應用之後我就知道我可以直接把pdf切成好幾塊丟到content裡面。

> 好啦我知道就算沒上這個課程我自己看api的docs也會知道有role跟content可以用

比較重要的另外一點是給了怎麼寫prompt的一個方向，有了這個方向之後就大概可以知道**好的prompt大概是長什麼樣子**，我想這點才是最重要的。


[7]: https://www.chatpdf.com/
[8]: https://github.com/binary-husky/gpt_academic

### 每個人都應該要有一個自己的chatGPT Library (using Jupyter?)

看完這課程之後，這是我最大的反思之一，既然chatGPT這麼唾手可得，我想我們要培養的一個習慣是，把這些好的prompts跟程式一點一點的把他寫得越來越習慣，並可以利用[Jupyter][9]等等的介面，要用的時候就很簡單的上去用就好。

舉例來說像上面的pdf應用，還有其他好多好多可以加快自己的生活過程的程式，都可以透過簡單的prompt engineering來完成，其中我想坊間可能會有很多類似的app提供各種各樣的功能，但自己寫的東西永遠都是最有靈活性的，擁有自己的chatGPT工具庫我想對自己的工作，或是能力上都會有一個很大程度的躍進。

[9]: https://jupyter.org/

# 結語

課程心得就到這裡了，拉哩拉雜寫了很多，都是我看這短短的課程的心得，裡面對課程內容的理解跟我自已的一些反思我覺得還蠻值得大家思考的，覺得有幫助的不妨把這篇文章轉發給各位好朋友們，也請務必按讚粉專(主要更新都在這裡！)或是訂閱我的youtube(裡面三不五時會開直播跟po各式影片)，還請各位多多關照！勞力！

粉絲專頁：[PP學習筆記][10]
youtube頻道：[Peter Kan][11]

[10]: https://www.facebook.com/pplearningnote/  
[11]: https://www.youtube.com/channel/UCJJrYsHT-akDO8gW5was_bg