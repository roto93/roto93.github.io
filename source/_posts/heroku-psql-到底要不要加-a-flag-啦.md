---
title: heroku psql 到底要不要加 -a flag 啦
date: 2021-09-08 01:21:15
tags:
---

<font size="2" color="#aaa">

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
所以現在確定了
只有在我的專案資料夾才能夠省略 --app

這時我突然想到
在當初第一次使用 heroku 的時候
有做了一些前置作業
其中包含了指定 remote
很有可能就是這個原因

```
> git remote -v
heroku  https://git.heroku.com/taiwan-astronomy-network-api.git (fetch)
heroku  https://git.heroku.com/taiwan-astronomy-network-api.git (push)
origin  https://github.com/roto93/TAN-API.git (fetch)
origin  https://github.com/roto93/TAN-API.git (push)
```

後來我也試著把 remote 去掉再試一次，這樣就不行了
看來可以確定主因了

## <font color="#f4a261">結論</font>

若專案資料夾有設定 heroku 的 remote
那在輸入 heroku 指令時就不需要加上 --app 的 flag


[參考](https://stackoverflow.com/questions/51815542/heroku-missing-required-flag-a)