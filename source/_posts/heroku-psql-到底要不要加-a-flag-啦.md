---
title: heroku psql 到底要不要加 -a flag 啦
date: 2021-09-08 01:21:15
tags:
---

<font size="3" color="#aaa">

## <font color="#f4a261">前言</font>

有件事一直搞不懂
我在看 heroku postgres 要怎麼備份時
看到有些範例會寫 --app 來指定 app 名稱
如: `heroku pg:backups:capture --app sushi`

看起來好像是必要的參數
但有時範例又沒有加

---

## <font color="#f4a261">尋找答案</font>

### 先看help

help flag 永遠是第一步
所以我輸入 `heroku pg:backups --help` 求解，得到

```
OPTIONS
  -a, --app=app        (required) app to run command against
  -r, --remote=remote  git remote of app to use
```

哦! 他說 required
但實際運行 `heroku pg:backups` 的時候
卻又沒問題

### 試試跟 app 有關的指令

於是接下來，我運行 `heroku apps` 得到

```
=== *******@yahoo.com.tw Apps
pi-first-api
taiwan-astronomy-network-api
```

沒錯
目前我的帳號有這兩個app
`pi-first-api`是我第一次使用heroku時的練習app
`taiwan-astronomy-network-api`這是我正在用的app
我輸入指令沒指定app
heroku卻知道我要使用哪個app
這可能代表我之前有做過什麼設定
讓heroku在「目前的資料夾當中」自動連結到正確的app上

### 是否只有此路徑能自動指定app?

為了證實剛剛的假設
我移動到上層資料夾(不在專案內)
執行 `heroku pg:backups` 
居然跳出錯誤訊息!
```
 »   Error: Missing required flag:
 »     -a, --app APP  app to run command against
 »   See more help with --help
```





[參考](https://stackoverflow.com/questions/51815542/heroku-missing-required-flag-a)