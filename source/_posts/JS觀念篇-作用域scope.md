---
title: JS 觀念篇1 -- 作用域 scope
date: 2022-07-28 21:48:07
tags:
---

<font size="2" color="#aaa">

## <font color="#f4a261">前言</font>

接下來幾篇會著重在一些 javascript 的重要觀念
未來如果忘了哪部分就可以回來看看

## <font color="#f4a261">作用域 Scope</font>

Scope 其實就是字面上的意思
一個變數或函式所能作用的範圍
在作用域以外的地方，就無法存取該變數
現在就用變數的宣告來解釋 scope 吧

在我開始學程式之前
曾經有個東西風靡一時
那就是`var`，函數變數
在我寫這篇文章之前，原本以為`var`是宣告全域變數
結果仔細一查才發現並不是
連現今常用的`let`和`const`也不僅僅只是區域變數而已
只要搞清楚它們的作用域範圍就會懂了

## <font color="#f4a261">var 的作用域</font>

var 用來宣告函數變數 function variable
意即其作用域是在宣告他的函數內
來看個簡單的例子

```javascript
const printText = ()=>{
  var text1 = 'Earth'
  console.log(`text1: ${text1}`)
  
  if (true){
    var text2 = 'Moon'
  }
  console.log(`text2: ${text2}`)
}
printText()

// "text1: Earth"
// "text2: Moon"
```

text1 能被順利印出很正常
但 text2 也印的出來就沒那麼直觀了
這正是因為用`var`宣告的變數，在整個宣告它的函數內都有效
就算是在迴圈中宣告也一樣
`var`變數不會被限制在迴圈的 block 中
但如果在父層函數內的話就行不通囉~ 如下

```javascript
const printText = ()=>{
  const innerPrint = ()=>{
    var text = 'Sun'
  }
  console.log(text)
}
printText()

// Throwing error
```

text 變數的作用域只在 innerPrint 函式內
超出這個 scope 就取不到了

話說那我們要怎麼宣告全域變數呢
只要在所有函式之外進行 var 宣告就可以了
只是要注意，不要再說「var就是宣告全域變數」囉

## <font color="#f4a261">let 和 const 的作用域</font>

`let`和`const`都是用來宣告「區塊變數 block variable」
他們的作用域相同，都是在區塊內
如迴圈的區塊，或`if`區塊
再來一個簡單的例子

```javascript
const printText = ()=>{
  let text1 = 'Earth'
  console.log(`text1: ${text1}`)
  
  if (true){
    let text2 = 'Moon'
  }
  console.log(`text2: ${text2}`)
}
printText()

// "text1: Earth"
// Throwing error
```

text2 就印不出來了
因為 text2 只能在 if statement 中生存
這就是超出作用域的例子

## <font color="#f4a261">var VS let/const</font>


另外補充一個`var`與`let`的不同之處是
`var`會將宣告上提 (hoisting)
只是不會初始化
可看看下列例子

```javascript
const printText = ()=>{
  console.log('inside function before var:', text)
  var text = 'Earth'
  console.log('inside function after var:', text)
}
printText()

// "inside function before var:", undefined
// "inside function after var:", "Earth"
```

雖然在宣告 text 前就取值
但只顯示`undefined`，並不會報錯
因為 text 已經被宣告了，只是沒賦值而已

反之，`let`和`const`則會有所謂的暫時執行死區（TDZ）
所以這樣寫會出錯

```
const printText = ()=>{
  console.log('inside function before var:', text)
  let text = 'Earth'
}
printText()

// Throwing error
```

有關 hoisting 就留到之後幾篇再說吧

## <font color="#f4a261">結論</font>

#### Scope: 作用域，變數或函式的作用範圍，也可以想成生存範圍
`var`變數的作用域在宣告時的函式內
`let`變數和`const`常數的作用域則僅限於宣告時的區塊內