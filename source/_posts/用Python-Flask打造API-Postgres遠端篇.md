---
title: 用Python-Flask打造API-Postgres遠端篇
date: 2021-08-02 11:46:57
tags:
---

<font size="3" color="#aaa">


## <font color="#f4a261">前言</font>

繼前一篇我們成功在本地端為我們的 flask api 串接資料庫
現在我們要在 Heroku 上做一樣的事情
恩... 應該說目標一樣
但要做的設定還蠻不一樣的
繼續往下看吧!

## <font color="#f4a261">在 Heroku 串接 API 和 Postgres</font>

首先我們需要在 Heroku 上啟用一個外掛
叫 "Heroku Postgres"
你可以開瀏覽器到 Dashboard
Resources > Add-ons > Heruku Postgres

<img src="postgres-5.jpg" width="80%">

接下來會詢問你要用什麼方案
稍微看了一下發現居然有每個月 16,000 美元的方案OAO
好扯喔 
記得在查資料的過程中看到有人說
「也許 Heroku 算不上便宜 (或許可以說它是最貴的)」
不過幸好我們的用量不高
TAN 也根本沒人要看吧! (誤
我們就選免費的ㄅ

<img src="postgres-6.jpg" width="60%">

以上這些步驟可以用一行命令完成:

`heroku addons:create heroku-postgresql:hobby-dev --app <heroku_app_name>`

最後的 --app 可以指定 app 名稱
如果你在 heroku 上有多個 app，指定一下比較不會出錯

再來你需要一組 uri 作為 SQLALCHEMY_DATABASE_URI 的設定值，執行
`heroku config --app <heroku_app_name>`
他就會給你一長串`postgres://.........`的 uri，但直接使用這組 uri 會出錯
我們稍微把它改一下，變成`postgresql://........`，這樣就可以了

打開`app.py`，把之前設定 SQLALCHEMY_DATABASE_URI 的部分改成
```python
ENV = 'prod'
if ENV == "dev":
    app.debug = True
    app.config["SQLALCHEMY_DATABASE_URI"] = 'postgresql://postgres:et0997@localhost/TAN_test_data'
else:
    app.debug = False
    app.config["SQLALCHEMY_DATABASE_URI"] = 'postgresql://.........'

app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
```

這邊我令了一個 ENV 變數判斷現在是開發階段還是成品階段
如要回到本地測試，只要把 ENV 設為 dev 即可

然後我也把 debug 的設定放到這段的 if statement 中
所以程式中原本的`app.run(debug=True)`也可以改回`app.run()`了


## <font color="#f4a261"></font>


## <font color="#f4a261">在 Heroku 建立 table</font>



