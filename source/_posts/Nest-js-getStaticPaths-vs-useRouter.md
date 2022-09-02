---
title: 'Nest.js: getStaticPaths vs useRouter'
date: 2022-09-01 22:52:49
tags:
---

<font size="2" color="#aaa">

## <font color="#f4a261">前言</font>

在學習生成動態頁面(dynamic routes)時
看到了兩種方法: `getStaticPaths`和`useRouter`
來整理一下他們之間的差異

## <font color="#f4a261">在 server side 生成動態頁面 getStaticPaths</font>

[getStaticPaths](https://nextjs.org/docs/basic-features/data-fetching/get-static-paths) 和 getStaticProps 一樣
是只會跑在 build time 的函式
假如我們的頁面數量是根據某個資料決定的
那是不是我們只要在 build time 之前
手中握有那份資料，然後告訴 Next 怎麼依照資料產生頁面
這樣就不用手刻好幾十個頁面檔案
這時就要用到`getStaticPaths`

```javascript
// in '/pages/[id].js'
export const getStaticPaths = async()=>{
  const res = await fetch('https://some-api')
  const data = await res.json()

  const paths = data.map(item=>{
    return{
      params:{id:item.id.toString()}
    }
  })

  return {
    paths, 
    //paths 必須是一個 array 包含數個 object，
    //每個 object 都長這樣: {params: {id:'xxx'}} <--- 註: key 是 id 因為檔名是[id].js
    //這些 object 會作為 prop 分別傳進 getStaticProps 中

    fallback: false
    //當 fallback 為 false 時，如果用戶進到不存在的 route 時，預設會顯示Next.js
  }
}
```

```javascript
// in 'pages/[id].js'
export const getStaticProps = async({params}) => {
  const res = await fetch(`https://some-api/${params.id}`)
  const data = await res.json()

  return {
    props:{ //這個 props 會傳進 page component 中
      data
    }
  }
}
```

以上動作會在 build time 的時候進行一次


## <font color="#f4a261">在 client side 產生動態資料 useRouter</font>

另一種情境是
可能我們的網站有連接資料庫，而那個資料庫時常更新
每當資料庫多出現一個 record 時(例如新增一篇貼文)
前端可能也需要多一個頁面來呈現它
但如果因此就需要重新 build 一次的話就太麻煩了
這時就可以用`useRouter`來達到動態頁面
簡單來說，頁面都是同一個
只是會根據 URL 來取得動態資料

```javascript
// in '/pages/[id].js'
import {useRouter} from 'next/router'

export default function Page(){
  const router = useRouter()
  const {id} = router.query 
  // 由於檔名中有 [id]
  // 所以實際 URL 中，'/pages/'之後的字串就會被當作 id params
  // 可藉由 useRouter().query 取得其值

  return (
    <div>
      <p>{id}</p>
    </div>
  )
}
```

以上動作會在 client side 的每次 request time 運行一次

## <font color="#f4a261">結語</font>

`getStaticPaths` 是在 build time 根據你定義的方法動態生成頁面

`useRouter` 是在 request time 根據瀏覽器的 URL 動態生成參數