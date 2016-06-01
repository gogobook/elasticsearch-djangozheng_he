# 關於讀後心得

## 1. 原文有些typo
嗯，就是typo, 在執行時會報錯，最大的一條是第一部分的django-skeleton的template的網址有typo，會讓你一開始就踢鐵板。

## 2. 其實很多東西有些難度
比如，class Meta: 的使用，再來像django-extention、model-mommy的使用，甚至elasticsearch的部分，其實都不算簡單。而且都滿有價值的。
class Meta: 在中國的翻譯叫「元選項」，但我覺得很怪，也許中文的文化裡，比較少這樣的東西，所以我稱其為「自觀照」，也有人稱「自省」，由於

## 3. suggest,自動補全，很棒！
然後我看了一下api 文件，看不懂? 有些太簡單了，看elasticsearch官網文件才發現好多資料喔！真的超棒的，但真的得花時間了解。教學的原文中使用suggest來達成自動補全的功能，但最後導向的，卻是由資料庫出來的資料。

### [Suggester](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-suggesters.html)
藉由使用一個suggester，suggester 功能可建議看起來相似的term。部分的建議功能仍在發展中。
suggest request part不只定義在`_search` request的query part，也可透過REST `suggest` endpoint來使用。
```js
curl -s -XPOST 'localhost:9200/_search' -d '{
  "query" : {
    ...
  },
  "suggest" : {
    ...
  }
}'
```

Suggest request使用`_suggest` endpoint應省略`suggest` element, which is only used if the suggest request is part of a search.
```js
curl -XPOST 'localhost:9200/_suggest' -d '{
  "my-suggestion" : {
    "text" : "the amsterdma meetpu",
    "term" : {
      "field" : "body"
    }
  }
}'
```
每一request可指定數種suggestions，每一種suggestion 被一個屬性名稱指定。在下面這個例子，2種suggestions 被requested。`my-suggest-1`及`my-suggest-2` 二個suggestions使用`term` suggester, 但有不一樣的文字。
```js
"suggest" : {
  "my-suggest-1" : {
    "text" : "the amsterdma meetpu",
    "term" : {
      "field" : "body"
    }
  },
  "my-suggest-2" : {
    "text" : "the rottredam meetpu",
    "term" : {
      "field" : "title"
    }
  }
}
```
每一個options arrary 含一個option object內含suggested text, 其document頻率及分數用以比對suggest entry text. 這意味得分與使用的suggester有關。The term suggester’s score is based on the edit distance.
```js
"options": [
  {
    "text": "amsterdam",
    "freq": 77,
    "score": 0.8888889
  },
  ...
]
```
### 直接跳Completion suggester
稍微看一下，原文應該是使用completion suggester。