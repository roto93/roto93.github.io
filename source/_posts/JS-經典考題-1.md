---
title: JS 經典考題 1
date: 2022-08-01 22:29:33
tags:
---

<font size="2" color="#aaa">

## <font color="#f4a261">前言</font>

前兩篇的 JS 概念篇，分別整理了 scope 和 closure
這篇就來整理一個 javascript 面試常見的題目

## <font color="#f4a261">經典問題</font>

```javascript
for (var i = 0; i < 5; i++) {
  setTimeout(function() {
    console.log(i)
  }, 0)
}

// 5,5,5,5,5
```
第一個重點是 
setTimeout 會將 console.log 函式放在暫存區
for loop 結束後一共會放五個 console.log 在那邊

第二個重點是 `var`
在上一篇中我們提到 var 變數的生存範圍是 function
所以`i`的 scope 就會在 for loop 之外
所以我們可以理解成 for loop 的每個循環
都會共用同一個`i` (指reference)

> <font color='gold'>閉包會參照環境變數值，而不是複製其值</font>

而最後當程式要來處理暫存區的任務時
由於 local variable 中沒有`i`
便往上找 function variable `i`
而 loop 也已經結束，`i`已經從 0 變成 5 了
可想而知就會印出五次 5

## <font color="#f4a261">解法1: let</font>

這題有很多解法
下面這個應該是最簡單的了吧

```javascript
for (let i = 0; i < 5; i++) { // 把 var 換成 let
  setTimeout(function() {
    console.log(i)
  }, 0)
}

// 0,1,2,3,4
```

當我們改用 let 時
`i`的生存範圍便限制在每個 loop block 當中
有幾個迴圈，`i`就會產生幾次
setTimeout 在 block 中找得到`i`，便會記住每個 loop 的`i`
而不會像原本一樣由於在區域變數中找不到
就往上層找函數變數
這是 ES6 之後的新解法
利用 let 生存範圍的特性

## <font color="#f4a261">解法2: 閉包</font>

我們可以利用閉包的特性鎖住每個`i`

```javascript
for (var i = 0; i < 5; i++) {
  const timer = (n) => { // 記得定義 parameter n
    setTimeout(function() {
      console.log(n)
    }, 0)
  }
  timer(i) // 記得傳入 i
}

// 0,1,2,3,4
```
每次運行`timer(i)`時會產生一個閉包
同時每次都會產生新的參數`n`
用來儲存當下傳入的`i`
所以會得到正確的結果

## <font color="#f4a261">解法3: IIFE</font>

Immediately-Involked Function Expression (IIFE)
立即呼叫函式表達式
定義函式之後立刻呼叫的寫法
它具有儲存當下環境的特性
是形成閉包的作法之一
以非常白話來解釋: 

> <font color="gold">讓函式立刻運行，而且不用取名</font>

```javascript
for (var i = 0; i < 5; i++) {
  (function(value) {
    setTimeout(function() {
      console.log(value)
    }, 0)
  })(i)
}

// 0,1,2,3,4
```

追根究柢，這其實跟方法二幾乎一樣
只是寫法不同而已

## <font color="#f4a261">結論</font>

這個題目 tricky 的部分在於
原本應該要利用閉包的特性鎖住每個`i`
卻又因為閉包只會參照位置
而導致非同步函式事後取到錯誤的值

本篇所用的三個解法的中心思想是

> <font color="gold">不要參照外部變數</font>

只要不要參照外部變數，讓每次迴圈都產生一個變數來用
便能避免事後才取到改變後的變數

老實說到現在還沒辦法肯定的說我清楚了
但藉由思考這個題目，對於 scope, closure 肯定會比以前更有概念
也更了解 javascript 的運作方式