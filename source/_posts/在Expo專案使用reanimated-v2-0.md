---
title: 在 Expo 專案使用 reanimated v2 基礎篇
date: 2021-06-30 13:48:38
tags:
---

## <font color="#BC2C1A">什麼是 reaimated ?</font>

reanimated 完整名稱是 react-native-reanimated
是一個專門用來做 react native 動畫的套件
在我寫這篇文的現在，最新穩定版本是 v2.2.0

---

## <font color="#BC2C1A">前言</font>

在過去 v1 時期，要學習還蠻不容易的
想當初我就是先接觸 v1，那時候我對 rn 和 js 的基礎都還不熟
看網路上的教學時一下看到用 Animated.Value，一下有人用 useRef.current
語法有點不一樣我就不行了，更不用說當時大多數人都還在用 class component
所以學得蠻挫折的，我到現在還不知道要怎麼用 v1 實作動畫

2020 年中推出的 v2 給了我一絲希望
他提供幾個方便的 hook，簡單幾行就可以實現動畫
太開心了
多虧了它我才能在菜鳥時期學會做一些不太複雜的動畫
(不過基礎還是應該要先會啦XD)

v2 版本也被很多 youtuber 推薦
其最大的優點在於能將動畫統一放到 UI thread 上執行
甚至提供一些 method 讓我們能操作 JS/UI thread
於是動畫就能變得非常流暢
我真的覺得一個 app 好不好用，順暢度真的佔了很大一部份

---

## <font color="#BC2C1A">安裝</font>

今天會想整理這篇是因為當初在安裝時耗了很多工夫
除了一直來來回回安裝
也很怕不小心把專案毀了回不去

這是因為我沒注意到不同 expo-cli 版本可以使用的 reanimated 版本是不一樣的
安裝前必須先確認好才行
不過現在已經沒有這個問題了

1. npm 安裝

```javascript
npm install react-native-reanimated
```

2. 打開`babel.config.js`

```javascript
module.exports = function (api) {
  api.cache(true);
  return {
    presets: ['babel-preset-expo'],
    plugins: ['react-native-reanimated/plugin'], // <-- 加上這行
  };
};
```

3. 重新啟動專案並清除 cache

再次啟動時不要執行`npm start`，而是

```
expo start -c
```

這步非常重要，當初不知道這個步驟的我浪費非常多時間
偏偏 doc 裡面也沒講 QQ

至此，只要安裝版本正確、有加 plugin、有清除 cache
就可以順跑ㄌ...我是說使用 reanimated 了

---

## <font color="#BC2C1A">初探 worklet</font>

worklet 是 v2 新發布的功能，介紹功能之前先讓大家看看它的用法

```javascript

const doSomething = (input)=>{
    // do something
}

const doSomethingWorklet = (input)=>{
    'worklet'
    // do something
}

```

跟一般的 function 幾乎沒差異
就只是在閉包裡頭第一行多寫一句`'worklet'`而已
當一個 fuction 被定義成 worklet，它就「能」被交由 UI thread 執行
至於什麼是 UI thread 嘛...

就定義而言，它是從原本的 javascript thread 中分離出來的一部份
你可以把 UI thread 看成是 JS 因忙不過來而請來的助理
顧名思義，UI thread 主要是用來處理畫面渲染的工作的

而 reanimated 引入 worklet 這個新用法正是為了讓動畫從 app 的運算中獨立出來
不要讓動畫被其他事情影響卡住，能夠順順跑

另外有發現我前面強調「能」由 UI thread 執行嗎?
這是因為如果真的要在 UI thread 執行，必須用`runOnUI`這個函式才行
如下:

```javascript
<Button onPress={()=>{
    runOnUI(doSomethingWorklet)(input) // doSomethingWorklet 將會在 UI thread 執行
}}>
```

那如果情況反過來，我想在 worklet 中執行 JS thread 怎麼辦?
方法很類似，只要用`runOnJS`就行了

```javascript
const doSomethingWorklet = () => {
    'worklet'
    runOnJS(doSomething)(input) // doSomething 將會在 JS thread 執行
}
```

值得一提的是這個`runOnJS`會蠻常碰到的
例如我在做匯率 APP 的時候有些按鈕同時兼顧動畫跟 setState
state 的變動就必須用`runOnJS`來執行了

---

## <font color="#BC2C1A">結語</font>

今天的超入門介紹就到這邊
雖然還沒講到能實作動畫的階段
但我覺得快速確實地安裝好套件
並搞清楚`worklet`、`runOnUI`、`runOnJS`的用法原理還是很重要的

下一篇介紹 reanimated 的文就會來說說`useSharedValue`和`useAnimatedStyle`怎麼使用!