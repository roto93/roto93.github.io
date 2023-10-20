---
title: 動態調整svg圖檔的URL和顏色
date: 2023-10-20 10:54:02
tags:
---

<font size="2" color="#999">

## <font color="#f4a261">前言: 如何顯示 svg 圖檔</font>

關於怎麼在 react 專案中顯示 svg
有下列幾種方法

1. 用 img 元素顯示
2. 利用 SVGR 線上轉換器，將 svg 轉換成 tsx 版本
3. 安裝 SVGR plugin，直接在專案中 import svg 就能用

### 1. 用 img 元素顯示

```jsx
// 直接放路徑
<img src={"./ic-close.svg"} />;

// 也可以import後再放
import CloseIcon from "./ic-close.svg";
<img src={CloseIcon.src} />;
```

### 2. 利用 SVGR 線上轉換器，將 svg 轉換成 tsx 版本

```svg
<!-- before -->
<svg width="17" height="17" viewBox="0 0 17 17" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M3.40137 3.3999L13.6014 13.5999M13.6014 3.3999L3.40137 13.5999" stroke="white" stroke-opacity="0.8" stroke-width="2" stroke-linecap="round"/>
</svg>
```

```tsx
// after
import * as React from "react";
const SvgComponent = (props) => (
  <svg
    xmlns="http://www.w3.org/2000/svg"
    width={17}
    height={17}
    fill="none"
    {...props}
  >
    <path
      stroke="#fff"
      strokeLinecap="round"
      strokeOpacity={0.8}
      strokeWidth={2}
      d="m3.401 3.4 10.2 10.2m0-10.2-10.2 10.2"
    />
  </svg>
);
export default SvgComponent;
```

### 3. 安裝 SVGR plugin，直接在專案中 import svg 就能用

安裝方法請見 https://react-svgr.com/docs/next/

```tsx
import Icon from './ic-close.svg'

...

<Icon/>
```

## <font color="#f4a261"> 前言: 如何動態調整 svg 的顏色</font>

#### 1. 手動編輯 svg 裡的 fill 或 stroke 屬性，改為 currentColor

```svg
<svg width="17" height="17" viewBox="0 0 17 17" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M3.40137 3.3999L13.6014 13.5999M13.6014 3.3999L3.40137 13.5999" stroke="currentColor" stroke-opacity="0.8" stroke-width="2" stroke-linecap="round"/>
</svg>
```

#### 2. 調整 SVGR 套件的設定，讓他再轉譯的過程中自動把某個顏色(例如#ffffff)換成 currentColor

細節請見：https://react-svgr.com/docs/options/#replace-attribute-value

## <font color="#f4a261">如何用`<img>`顯示卻又能動態調整顏色</font>

這次遇到的狀況是：
我要開發手機版網頁的 bottom nav bar，長這個樣子

<img src="1.png" width="60%"/>

由於這些按鈕必須和桌面版的 header nav bar 同步
秉持 single source of truth 的原則
我先建一個 json，然後在兩處 UI insert value，來決定要顯示什麼圖、文字跟點擊後去到的路徑

```json
// NavRoutes.json
[
  {
    "title": "Product",
    "href": "/",
    "icon": "ic-product.svg"
  },
  {
    "title": "Bridge",
    "href": "/bridge",
    "icon": "ic-bridge.svg"
  }
]
```

```tsx
import NavRoutes from 'NavRoutes.json'
import Image from 'next/image'

...
{NavRoutes.map(route=>(
  <Image src={route.icon}/>
))}
```

問題是這樣就無法利用 currentColor 來調整顏色了
我還要讓網頁所在的按鈕亮起來才行

以 svg 來說因為檔案很小
其實我可以準備兩份顏色不同的 icon
在 active/inactive 之間切換就好
但我還是好奇有沒有更省事的做法
於是找到這個[討論](https://stackoverflow.com/a/71542230)
發現一個蠻酷的做法

它的原理是利用 CSS 的 filter 屬性改變圖片的顏色
只是這個轉換過程不好計算，所幸有人已經寫好[轉換工具](https://codepen.io/sosuke/pen/Pjoqqp)了
唯一要注意的是這個轉換必須從黑色開始
要嘛轉換前先把 svg 手動改成黑色
不然就是在 css filter 開頭加上 `brightness(0) saturate(100%)` 也行

## <font color="#f4a261">References</font>

- https://stackoverflow.com/a/71542230
- https://codepen.io/sosuke/pen/Pjoqqp
