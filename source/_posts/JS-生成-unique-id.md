---
title: JS 生成 unique id
date: 2021-09-26 14:12:12
tags:
---

<font size="3" color="#aaa">

## <font color="#f4a261">前言</font>


在設計資料庫結構時，常常會用到 id 的概念
id 主要用來辨認資料
因此它的唯一性是很重要的

之前我在定義postgre資料庫的 id 欄位時，
是直接設為 PK (在 Flask_SQLAlchemy 中 PK 預設 auto increment)

這樣有個小缺點是
id 好猜，且會反映各資料間的先後關係
但 id 並不會作為排序依據
所以我認為並不適合做整數自動遞增

不過好處是 api endpoint 不會太難看XD
但應該沒什麼人會在意 endpoint 啦

## <font color="#f4a261"> UUID vs GUID </font>

查資料的過程中我看到這兩個詞

- UUID (Universally Unique Identifier) 通用唯一辨識碼

- GUID (Globally Unique Identifier) 全域唯一辨識碼


UUID是在分散式計算環境(Distributed Computing Environment, DCE)下
確保 ID 不重複的一個規範
而 GUID 則是由微軟設計的 UUID 應用
編碼方式有所不同

UUID格式：xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx (8-4-4-16)

GUID格式：xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (8-4-4-4-12)

總而言之
> GUID 可以視為 UUID 的一個變體
> 是微軟基於 UUID 再加上一些自己的方法做出來的 ID

## <font color="#f4a261"> 那麼...哪裡才能買的到呢? </font>

不用買，各平台都有實作 UUID 的套件了
以我在用的 JS 來說
可以用這個 npm 的 [uuid](https://www.npmjs.com/package/uuid)

使用方法很簡單

```
npm install uuid
```

```
import uuid from 'uuid'

uuid.v4() // 生成一組 uuid
```

最後補充一下這裡的v4是什麼意思(取自wiki)

版本1 - UUID 是根據時間和節點ID（通常是MAC位址）生成；

版本2 - UUID 是根據識別碼（通常是組或使用者ID）、時間和節點ID生成；

版本3、版本5 - 確定性UUID 通過雜湊（hashing）命名空間（namespace）識別碼和名稱生成；

版本4 - UUID 使用隨機性或偽隨機性生成。