---
title: Javascript 在不同函式定義方式中使用 this
date: 2021-04-16 09:27:21
tags:
---

<font size="2" color="#aaa">


## <font color="#BC2C1A"> 前言</font>

在使用物件導向程式設計時，一定會碰到`this`(在python裡是`self`)

要百分之百了解`this`的作用原理其實談不上容易，但簡單來說：

> 在一個 class 中呼叫`this`時，`this`指的是那個 class 本身

或是更 general 一點：

> 誰呼叫到`this`，`this`就是誰

但最近遇到一個情形，呼叫this的時候找不到理應存在的變數



## <font color="#BC2C1A"> 情形</font>

這是一個 IconDisplayer 物件
調用 display() method，回傳 <`Image>`

```javascript
const IconDisplayer = {
    Current: {
      USD: <Image source={require('./src/image/nations/united-states.png')} />
    },
    display: () => {
      if (currency === 'USD') { return (this.Current.USD) }
    }
  }
```

運行時他會告訴你：

<font color="red">TypeError: Cannot read property 'Current' of undefined

意思就是說，程式找不到 Current 這個變數
但是很奇怪，明明就定義在物件裡Ｒ，也用了this
應該找得到才對呀

---

## <font color="#BC2C1A"> 原因</font>

後來查了一下發現一切都是 arrow function 的緣故

- #### MDN & W3Schools:
	- In arrow functions, this retains the value of 
	the enclosing lexical context's this.
	- Arrow functions do not have their own this.
	- In global code, it will be set to the global object.

如果是呼叫以前的傳統函數，每次都會建立一個環境 (Function Execution Context)來運行，並把當時的呼叫者令為this，誰呼叫 誰就是this

但定義 arrow function 時不會有這個流程
因此在其中使用this時，指的便會是外層所使用的 global this，在 HTML 裡就是 window

例如上述的例子：我們只要把this改成IconDisplayer就可以了

```javascript
    display: () => {
      if (currency === 'USD') { return (this.Current.USD) }
    }
```

不然就要用傳統函式：

```javascript
    display: function(){
      if (currency === 'USD') { return (this.Current.USD) }
    }
````

---

## <font color="#BC2C1A"> 結語</font>

曾經看到有人說應該要把 arrow function 視為獨立的新語法，而不是傳統function 的變化，因為有時運作方式是截然不同，今天更能體會到這點了！
最後，今天的重點：

> this 的值只跟「你如何呼叫、誰呼叫」有關，與程式碼位於哪裡完全無關


參考：[iT邦幫忙](https://ithelp.ithome.com.tw/articles/10207992)
