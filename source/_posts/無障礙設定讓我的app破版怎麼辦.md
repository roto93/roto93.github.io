---
title: 無障礙設定讓我的app破版怎麼辦
date: 2022-02-25 17:06:47
tags:
---

<font size="2" color="#aaa">

## <font color="#f4a261"> 前言 </font>

前幾天跟高中同學去日月潭玩
沒錯是同學會!
沒想到高一到現在已經九年半了
居然還能一起出門過夜
實在是非常難得的友誼

其中有些同學有在關注我的限動
所以對我的app滿好奇的
我就趁機請他們上 google play 下載來測試看看
也因此發現了一個嚴重的破版問題
如圖

<div style="display:flex; justify-content:space-between">
<img src="./破1.jpg" width="40%"/>
<img src="./破2.jpg" width="40%"/>
</div>

可以看到是因為字體太大而導致的破版

## <font color="#f4a261">原因: 無障礙設定</font>

自從開始用別人的手機測試後
才知道原來還需要考慮無障礙設定

無障礙設定是為了照顧到一些身體機能不足的用戶
在手機上不外乎就是改變字體、對比、或是朗讀模式等等
而這次的case
就是因為我同學的手機把系統字體調大了才會這樣

<img src="./3.jpg" style="width: 40%;min-width: 250px;"/>

雖然是個貼心的設定
但對app開發者來說是一大麻煩
因為字體改變意味著版型要跟著調整
也幾乎沒有辦法確保有一個各種字體都好看的排版方式
最終的解法可能有兩種
1. 極為簡單的排版，字再大都還算可接受
2. 禁止部分或全部的字體縮放

## <font color="#f4a261">禁止app字體縮放</font>

我們決定採用第二種方法
雖然這意味著可能會減少眼睛不好的用戶(如長輩)
但我們原先鎖定的客群就是比較偏向年輕用戶
所以並沒有偏離初衷

react native 的 Text 和 TextInput 元件都有提供一個屬性叫做 allowFontScaling
只要設為false就能阻止字體被改

<img src="./4.jpg" width="80%" />

不過如果還要修改app裡的所有元件
真的有點麻煩
其實只要在APP的最上層(如App.js)
直接設定元件的defaultProps
就可以一次更改所有元件囉
如下:

```js
Text.defaultProps = {
  allowFontScaling: false
}
TextInput.defaultProps = {
  allowFontScaling: false,
  underlineColorAndroid: "transparent"
}
```

補充一點
當我把TextInput設為allowFontScaling時
不知道為什麼突然冒出底線
所以必須多加一個`underlineColorAndroid: "transparent"`的設定

調整完後，正常的版面

<div style="display:flex; justify-content:space-between">
<img src="./1.jpg" width="40%"/>
<img src="./2.jpg" width="40%"/>
</div>

## <font color="#f4a261">結論</font>

無障礙設定給人方便 
也帶來一點不方便
往後在思考排版的時候
也要記得考慮到無障礙設定才行

</font>