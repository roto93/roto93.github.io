---
title: 試玩Highcharts遇到的遇到的TS小問題
date: 2023-08-20 01:47:00
tags:
---

<font size="2" color="#999">

## <font color="#f4a261">前言</font>

最近安裝 highcharts 來試玩，因為之後公司的網頁有可能會換圖表套件，想要找一個比現在用的 lightweight chart 再更靈活一點的，畢竟他的主力是在 financial charts，拿來做股市圖蠻不錯的，但圖表類型很少連圓餅圖都沒有。

研究的過程中遇到一個 type error 覺得蠻有意思的。

## <font color="#f4a261">想要從圖表提取 x, y 以外的資料</font>

我想要做的功能是：當滑鼠指到圖上的任一點，圖表外的某處就要顯示該點的 x,y 值，以及另一個附加的資料。類似下面這樣(lightweight chart)

<img src='1.png' width='80%'/>

這張圖看起來不就是把 y 值提出來而已嗎？這樣有什麼困難？但其實我背後是把 y 值設為幣價的絕對值，以此來做圖。至於 y 軸會變成百分比是因為 lightweight chart 有提供一個功能讓我可以用百分比來顯示。

現在我要用 highcharts 做到一樣的事情，在設定 data property 時應[用物件的方式定義 x,y 以及額外的資料](https://www.highcharts.com/forum/viewtopic.php?t=48914#p177391)，而不是用陣列的方式定義，請看以下

```jsx

const options {
  data: [[1,20,'extra1'], [2,23,'extra2'], [3,19,'extra3']], //不要這樣定義
  data: [
    {x: 1, y: 20, extra: 'extra1'},
    {x: 2, y: 23, extra: 'extra2'},
    {x: 3, y: 19, extra: 'extra3'}
  ] //應該這樣定義
}

```

這樣一來就可以用`series.point.event.mouseover`觸發 hover event ,再藉由 callback function 裡的`this`去取值，但這就遇到 Typescript 的問題了

<img src='2.png' width='80%'/>

## <font color="#f4a261">如果 this 跟想像的不一樣怎麼辦</font>

我想這是因為我定義了額外的屬性，雖然 highcharts 好心的幫我加進去 this，但是新的AUM屬性就是不存在在this的type(實際上是Highcharts.Point)裡面啊，但我要怎麼讓這個 type error 消失啊？ 

optional chaining 不行，this 也不能 assign 到別的變數(`no-this-alias` error)

<img src='3.png' width='80%'/>

研究好一段時間之後，這樣就可以了：

```js
{
  AUM: (this as typeof this & { AUM: string }).AUM
}
```


## <font color="#f4a261">結論</font>

一般來說遇到這種問題應該要去修改最根本的 type 定義，但今天遇到的是特殊狀況，我要怎麼從源頭修改 this 的 type 啊?
現在終於知道原來可以[在 dot notation 前面用括號](https://stackoverflow.com/questions/18083389/ignore-typescript-errors-property-does-not-exist-on-value-of-type)刮起來用`as`，又多知道一個應急的方法了



