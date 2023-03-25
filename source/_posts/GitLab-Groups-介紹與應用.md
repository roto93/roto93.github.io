---
title: GitLab Groups 介紹與應用
date: 2023-03-25 14:43:17
tags:
---

<font size="2" color="#999">

## <font color="#f4a261">前言</font>

老闆總是覺得前端很簡單，總以為套套版三天就可以上線了
但我能理解，因為國中時我也常把任何事情想得很簡單
沒做過的事都不會知道有什麼難處
所以我並不會對國中生要求太多

但如果能把事情快速做完有何不可？
無奈這間公司的每個網站都是前輩們趕鴨子上架
缺乏一個有系統的管理方式
因此我想來試試看

## <font color="#f4a261">用 Group 來管理專案</font>

我想做的主要有三件事情

1. 整合公司 GitLab 內的所有前端專案
2. 將每個專案中會共用的元件、函式提出來，變成 library 
3. 建立 template

Group 是 GitLab 中的一個功能
你可以把相關的專案放在同一個 group 當中
此時專案的 namespace 就會從
<img src="without-group.png" width="320"/>
變成
<img src="with-group.png" width="480"/>
除了一目了然，一下就知道這是前端專案，或是前端模板
最大的優點就是權限控制了

我們試想一個情境
公司內有老闆、PM、RD、Dev Leader、QA
就算每個人都能看到每個專案
每個人應賦予的權限也不一樣
RD 要能編輯，QA 要能開 issue，老闆動口不動手
只要有用 Group 將各個專案進行分類
就不需要對每個專案都設定一次
而是以 Group 為單位來給予權限

- Owner: 權限最大，掌管專案的生死
- Maintainer: 通常為資深工程師，負責處理 branch 的異動
- Developer: 開發人員，專心寫 code
- Reporter: 測試並回報問題，所以要能開 issue
- Guest: 只能看，老闆就在這了啦

## <font color="#f4a261">用 Group 來管理專案</font>

目前公司只有我一個前端，所以權限這件事目前還不需要煩惱
我只要先整理好 Group 的架構就好

首先建立一個名為 Frontend 的 Group
只要是前端的專案都放進來
接著 Frontend Group 底下再建立兩個 sub-group
Templates 和 Shared

Templates 裡面會放一些做好初始設定網站作為模板
包括常用的套件也都會安裝進去
未來要開發新網站就只要從這些模板開始就好
因此我先做了兩個模板
如果需要 SEO，就用 Nextjs 的模板
如果不需要 SEO，或是一次性的網站，就用 Vite 的模板

Shared 裡面放的就是跨專案共用的檔案啦
我分別建立了 component, stylus, utils 三個專案
並以 submodule 的形式加進 template 當中
這樣的好處是，可以維持網站間的一致性
例如每個網站都有一樣的 footer
那我就只要修改 Shared/components 這個 submobule
再打開每個專案更新 submodule，重新跑一次 CICD
就不用怕忘了改(或改錯)某個網站的 footer 了

大至上的架構是這樣，中括號代表 group，其他的就是 repo：

```
[Frontend]
  [Templates]
    Nextjs-web3-template
    Vite-web3-template
  [Shared]
    component
    stylus
    utils
  Website 1
  Website 2
  Website 3
```

## <font color="#f4a261">結論</font>

利用 Group，我們便能將各個 repo 分類
光看 namespace 就知道專案的類型
也能用更方便的方式管理專案權限
