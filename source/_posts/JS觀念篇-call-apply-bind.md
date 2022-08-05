---
title: 'JS觀念篇-call, apply, bind'
date: 2022-08-05 12:01:55
tags:
---


<font size="2" color="#aaa">

## <font color="#f4a261">前言</font>

今天要介紹的 JS 觀念是 call, apply 和 bind 三個 function
這些我目前為止都沒用過
因為在過去的專案中，幾乎沒用到 OOP (Object Oriented Programming) 
class 也沒用過
自然就沒遇到適合的使用情境
但有時還是會在一些教學影片看到他們的身影
(即使那不是影片主題)
我就覺得應該要來補一下

## <font color="#f4a261">this 簡介</font>

`this`是在 javascript 中的重要語法
他指向的是函式被調用的位置

> 如果是物件調用函式，`this`就是那個物件
> 在瀏覽器中直接印`this`，就是`window`物件
> 在node.js直接印`this`，就是`global`物件
> 在嚴格模式中直接印`this`，就是`undefined`

如果我們想為物件打造一個 function
就可以在函式中用上`this`

```javascript
const newFriend = {name: 'Josie'}
const greeting = function(myName){
  console.log(`Hi, ${this.name}, my name is ${myName}`)
}
// greeting(???)
```

這時突然發現物件沒有進入點
解決方法可以把 greeting 定義在物件裡面
或是物件之上如果有一個類別的話
也可以定義在prototype裡面
但我們也可以改用 call, apply, bind 來呼叫函式
接下來就來一一介紹

## <font color="#f4a261">call</font>

call 的功能是執行函式，並指定函式中的`this`是誰

```javascript
// function.call(object, arg1, arg2, arg3, ...)
// 執行 function，傳入參數 arg1, arg2 ...，並指定函式中的 this 為 object

const newFriend = { name: 'Josie' }

const greeting = function(myName, otherName) {
  return console.log(`Hi, ${this.name}, my name is ${myName}. And this is ${otherName}.`)
}

greeting.call(newFriend, 'Tony', 'Bowen')


// Hi, Josie. My name is Tony. And this is Bowen.
```

## <font color="#f4a261">apply</font>

基本上跟 call 做一樣的事情
但提供另一個傳入引數 (argument) 的方式
改成傳入包含所有引數的陣列

```javascript
// function.apply(object, [arg1, arg2, arg3, ...])
// 執行 function，傳入參數 arg1, arg2 ...，並指定函式中的 this 為 object

const newFriend = { name: 'Josie' }

const greeting = function(myName, otherName) {
  return console.log(`Hi, ${this.name}, my name is ${myName}. And this is ${otherName}.`)
}

const arr = ['Tony','Bowen']

greeting.apply(newFriend, arr)

// Hi, Josie. My name is Tony. And this is Bowen.
```

## <font color="#f4a261">bind</font>

將函式中的 this 綁定給某個對象，然後回傳一個新的函式供使用

```javascript
// function.bind(object) 
// return function
// 將 function 和 object 綁定，回傳一個新 function

const newFriend = {name: 'Josie'}

const greeting = function(myName, otherName){
	return console.log(`Hi, ${this.name}, my name is ${myName}. And this is ${otherName}.`)
}

const greetToNewFriend = greeting.bind(newFriend)

greetToNewFriend('Tony','Bowen')

// Hi, Josie, my name is Tony. And this is Bowen.
```

## <font color="#f4a261">結論</font>

`call`和`apply`: 執行函式並指定`this`所指對象
`bind`: 回傳一個新函式，是原函式與`this`所指對象綁定的版本

基本上除非函式中包含`this`
否則不太會用到這三個函式