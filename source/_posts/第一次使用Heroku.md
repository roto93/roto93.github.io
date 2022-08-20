---
title: 第一次使用Heroku
date: 2021-08-01 12:34:20
tags:
---

<font size="2" color="#aaa">

## <font color="#f4a261">前言</font>

寫到這才發現我之前漏了 heroku 的介紹...
趕快補一下


## <font color="#f4a261">Heroku 設定</font>

heroku 是一個雲端平台，意思是他提供我們一個在網路上的空間，可以存放資料
更重要的是，你可以利用它部屬網站、API，這樣你就能用網址連線到你的網站了

有一個在 heroku 不斷出現的詞彙叫做 Dyno
Dyno 是運行和響應請求的應用程序的實例，欲執行網站就至少要一個 Dyno
以我目前的理解，可以把它當作 heroku 版的 repo

在免費方案下，
1. Dyno 有 550 小時/月的免費時數，若通過信用卡驗證，額外贈送 450 小時/月，總共免費時數為 1,000 小時/月，若多個 Dyno 執行則可分攤掉時數
2. Dyno 在 30 分鐘內沒有任何流量，系統自動進入睡眠狀態，睡眠狀態下則不消耗時數
3. 睡眠狀態下 Dyno 收到請求，等喚醒約20秒啟動系統
4. 儲存空間為 512MB

我已經在 Heroku 上建立帳號，建立新 app 叫做 taiwan-astronomy-network-api
然後便可繼續以下的步驟

## <font color="#f4a261">一些設定檔</font>

三份檔案要建立
`runtime.txt` 放的是這份專案所使用的 python 版本
```python
python-3.9.6
```
`requirements.txt` 放的是這份專案所需要的模組
執行 `pip freeze > requirements.txt` 即可

`Procfile` 是heroku運行所需的檔案，注意沒有副檔名，P要大寫
```python
web gunicorn app:app
```
第一個 app 對應的是 app.py 檔名的 app 
第二個 app 對應的是 app.py 中 `app = Flask(__name__)` 的這個 app

這三個檔案稍有不對就會出錯
所以在繼續往下走前一定要再三確認

## <font color="#f4a261">部屬專案</font>

先到[這裡](https://devcenter.heroku.com/articles/heroku-cli)安裝 heroku-cli

接著部屬專案的步驟如下:

`heroku login`

`heroku git:remote -a <app名稱>`

```
$ git add .
$ git commit -am "make it better"
$ git push heroku master
```
這邊不一定要用 master 要看你本地 repo 是在什麼 branch 上

到這邊應該沒問題了，趕緊下一篇就來串接資料庫!
