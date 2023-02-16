---
layout: post
title: "Javascript/Typescript 數字年月轉國字年月 |"
subtitle: ""
date: 2023-02-16
author: "Peter"
header-img: "img/post-bg-2022.jpg"
tags: [web]
---

剛好有個機會需要在網頁上用到數字的月份跟年份轉成民國的月份跟年份，但都有點太多功能，尤其是會[把十位跟百位加進去][1]，又或是[整個年月日都轉起來][2]。

因應這些，我簡單搞了一個大概轉到999都沒什麼問題的專門給民國年跟月的轉換，就貼在這裡。

```typescript
/**
 * Convert arabic digits to Zh digits, specificly used on year and months
 * @param {number | string} digit arabic string or number to convert
 * @example
 *
 * toZhDigit(110)
 * => "一一〇"
 */
function toZhDigit(digit) {
    // setup ZH number array
    var zhMap = ["〇", "一", "二", "三", "四", "五", "六", "七", "八", "九"];
    
    // check the type and modify it to string
    digit = typeof digit === "string" ? digit : digit.toString();
    
    // cut the number into small arrays
    var digitArray = digit.split("");
    
    // map the digit with zhArray
    var zhArray = [];
    digitArray.forEach(function (digit) {
        
        // change the second digit to "十" if there're only two digit
        if (digitArray.length === 2 && zhArray.length === 0 && digit === "1") {
            zhArray.push("十");
            return;
        }
        
        // return the array if the number is "10"
        if (digitArray.length === 2 && zhArray[0] === "十" && digit === "0") {
            return;
        }
        
        var numDigit = parseInt(digit);
        zhArray.push(zhMap[numDigit]);
    });
    
    // convert the array back to string again
    var result = zhArray.join("");
    return result;
}
```

由於這個是用`typescript`寫的，如果不會`typescript`的同學請用`tsc toZhDigit.ts` 就能轉成`javascript`了。


[1]: https://juejin.cn/post/6844903473255809038
[2]: https://blog.csdn.net/padavan/article/details/126292299