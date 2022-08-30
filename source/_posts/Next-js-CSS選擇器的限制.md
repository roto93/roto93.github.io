---
title: Next.js CSS選擇器的限制
date: 2022-08-30 01:15:21
tags:
---

<font size="2" color="#aaa">

## <font color="#f4a261">前言</font>

最近求職的過程中
看到幾間公司有將 Next.js 列在加分項目
因此提升了我對 Next.js 的好奇心

其實我在滿久以前就聽過 Next 了
只知道是 React 的加強版
針對 React 天生不利於 SEO 的缺點提供解決方案
而且不需要依賴 react-router 就能做到換頁
所以最近正式的來學習一下

途中在 CSS 上遇到了小問題

## <font color="#f4a261">在 Next 中使用 CSS 的方法</font>

先不論 SASS, CSS-in-JS 這些用法
如果要用正統 CSS 的話
有以下兩種方法

#### 1. 在 `_app.js` 中引入 global CSS

在 Next 專案中
只有一個地方可以引入純 CSS
就是`_app.js`，如下

```javascript
// In _app.js
import '../styles/global.css' // <--- Here!

export default function MyApp({Component, pageProps}){
  return <Component {...pageProps}>
}
```

如果在其他元件中`import 'xxx.css'`的話
會出現以下 error

> ./styles/global.css
> Global CSS cannot be imported from files other than your Custom <App>. Due to the Global nature of stylesheets, and to avoid conflicts, Please move all first-party global CSS imports to pages/_app.js. Or convert the import to Component-Level CSS (CSS Modules).
> Read more: https://nextjs.org/docs/messages/css-global


#### 2. 在各別頁面或元件中使用 CSS module

CSS module 也是一個見了許久卻沒使用的東西
他主要的功用是 能依照作用範圍切分 CSS 文件的大小

```css
/* In page.module.css */
.container {
  padding: 0 1rem;
  margin: 3rem auto 6rem;
}
```

```javascript
// In page.js
import styles from './page.module.css'  // <--- Here!

export default function page(){
  return (
    <div className={styles.container}>  {/* <--- Here!*/}
      <h2>This is a page.</h2>
    </div>
  )
}
```

上面的例子，`page.module.css`就是專門寫給`page.js`用的
這樣就可以避免跟其他元件的 class 撞名

以前當我要避免撞名時
不是把 APP 的所有 CSS 都寫在一份檔案
就是極致利用 BEM 規則把每個 class 都寫得超長

用了 CSS module 後
當你去看網頁上實際的 class 會發現他可能長這樣
`<div class="container__fbLkO"></div>`
CSS module 會在 class 後面加上亂碼
避免跟其他元件的 class 撞名



## <font color="#f4a261">一些限制</font>

進入正題(前面講了好多別的)
CSS module 雖然方便
但仍然有一些限制
例如在`.module.css`中
只能用 class 或 id 選擇器
不能用 element 選擇器

意思是不能在`.module.css`中這樣寫

```css
p {
  color: red;
}
```

詳細原因可以參考[這裡](https://github.com/vercel/next.js/discussions/16050#discussioncomment-49022)

如果基於某些原因必須去選 element
就只能寫在 global.css 中囉

除此之外，偽類偽元素以及選擇器的組合都還是能正常使用的喔

## <font color="#f4a261">結論</font>

我覺得不管是什麼前端技術(或程式語言技術)
最終都會向元件化靠攏
像 styled component 或今天介紹的 CSS module
都是盡可能的把「屬於元件的部分」跟元件本身放得越近越好
享受方便性的同時，也要同時注意使用上的限制才行
