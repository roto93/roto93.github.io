---
title: Recoil 簡介
date: 2023-04-17 11:05:27
tags:
---

<font size="2" color="#999">

## <font color="#f4a261">前言</font>

說到 React 的狀態管理工具
之前只有在線上課程學到 Redux
但由於之前的專案只需要用到 global state 而已
甚至不需要 useReducer 的功能
加上 Redux 在設定上還蠻複雜的
有 store, action, reducer, slice 等概念
讓我一直覺得大材小用的感覺
每次要用到新專案
基本上都要把 doc 從頭讀一遍ＸＤ

直到後來在公司接手外包前端的專案
發現他用了 Recoil 這個狀態管理工具
起初還有一點小排斥
但當我理解用法後
簡直開闢了新天地

## <font color="#f4a261">Recoil</font>

facebook 開發的 React 狀態管理工具
採用 atom 的架構，跟 Redux 的 flux 架構不同

相較於 Redux 輕量很多
很多時候我們只是想要單純使用 global state
而不打算用到 middleware 或是 reducer, actions 來管理狀態變化。
這時 Recoil 就非常方便。

## <font color="#f4a261">初始化</font>
要使用 Recoil 前置作業非常少
`npm install recoil` 安裝之後
用 RecoilRoot 包住整個 app
這樣就好ㄌ

```jsx
import {RecoilRoot} from 'recoil'

...

<RecoilRoot>
  <App/>
</RecoilRoot>
```


## <font color="#f4a261">Atom</font>

狀態的最小單位，初始化時需給予一個唯一的key以及初始值。
元件可利用 `useRecoilState` 或 `useRecoilValue` 來 subscribe 任一 atom
當這個 atom 發生變化時
所有 subscribe 它的元件都會更新。

```js
const nameState = atom({
	key: 'nameState',
	value: ''
})

const [name, setName] = useRecoilState(nameState)
```

## <font color="#f4a261">Selector</font>

一個 pure function
傳入值可以是 atom 或是別的 selector。

Selector 用來處理 derived data
根據 state 計算而得的值。讀值時使用 `useRecoilValue`。

```js
const fontSizeLabelState = selector({
  key: 'fontSizeLabelState',
  get: ({ get }) => {
    const fontSize = get(fontSizeState);
    const unit = 'px';
    return `${fontSize}${unit}`;
  },
});

const fontSizeLabel = useRecoilValue(fontSizeLabelState)
```

## <font color="#f4a261">心得</font>

Recoil 實在是很方便
包上 `<RecoilRoot>`，建立 atom，立刻就能用

官網：[https://recoiljs.org](https://recoiljs.org)
