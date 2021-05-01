---
title: 在firebase部署你的react專案
date: 2021-04-23 09:01:02
tags:
---

***

最近在學 react，希望可以每次完成一個 mini project 的時候就能放在網路上
這樣出門在外隨時可以向人展示 
<font color="gray">(雖然可能也沒什麼向人展示的機會QQ)

稍微查了一下各種部署react網站的方法，發現firebase可以說是最快的
步驟如下：

***

1. 到firebase創帳號，建立專案

2. 在 firebase console 中，進到 hosting 頁面，他會請你安裝

```
npm install -g firebase-tools
```

3. 接著到 react 專案資料夾，先登入

```
firebase login
``` 

4. 然後初始化

```
firebase init
```

5. 初始化過程中會問你一系列問題： 
	- 問你要啟用什麼功能：選 hosting
	- 問你要連結到哪個 firebase project
	- 問你要用哪個資料夾作為網站的 public 資料夾：輸入 build(或你定義的資料夾名稱)
	- 問你是不是 single-page app (SPA) 這個都選是應該沒問題
	- 如果你之前有執行過`npm run build`他可能會問你要不要覆寫index.html，選要

6. 完成後你會在專案裡看到 firebase.json, .firebaserc 兩個檔案

7. 如果還沒 build 過，執行`npm run build`

8. 執行 firebase deploy

9. 到 firebase hosting console 可以看到你的部屬紀錄，可以回到之前的版本 

10. 你也可以在本地開伺服器，只要執行`firebase serve`就可以了

*** 

第一次做的時候網站馬上就發布了，但第二次隔了一些時間才發布

害我還到處查為什麼是哪個步驟不對QAQ 結果稍微等一下就看到網站了哈哈

所以如果你沒看到馬上發布不要慌XD