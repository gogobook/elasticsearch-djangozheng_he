# 關於讀後心得

## 1. 原文有些typo
嗯，就是typo, 在執行時會報錯，最大的一條是第一部分的django-skeleton的template的網址有typo，會讓你一開始就踢鐵板。

## 2. 其實很多東西有些難度
比如，class Meta: 的使用，再來像django-extention、model-mommy的使用，甚至elasticsearch的部分，其實都不算簡單。而且都滿有價值的。
## 3. suggest,自動補全，很棒！
然後我看了一下api 文件，看不懂? 有些太簡單了，看官網文件才發現好多資料喔！真的超棒的，但真的得花時間了解。
### Suggester
藉由使用一個suggester，suggester 功能可建議看起來相似的term。部分的建議功能仍在發展中。
suggest request part不只定義在`_search` request的query part，也可透過REST `suggest` endpoint來使用。

