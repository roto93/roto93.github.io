---
title: 用Python-Flask打造API-Postgres遠端篇
date: 2021-08-08 11:46:57
tags:
---

<font size="2" color="#aaa">


## <font color="#f4a261">前言</font>

繼前兩篇我們成功在本地端為我們的 flask api 串接資料庫
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

用 --app 這個 flag 可以指定 app 名稱
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

## <font color="#f4a261">在 Heroku 建立 table</font>

先確定你有依照上一篇的教學將專案 deploy 到 heroku 上
執行 `heroku run python` 後輸入指令
```python
from app import db # 從 app.py 中引入 db 模組
db.create_all() # 在 app.config 中所指定的 database 中建立 table
```
如果要刪除 table 就用`db.drop_all()`

完成，之後就能夠用 ORM 指令操作 database 了!

## <font color="#f4a261">在 heroku 上用 psql 檢視資料</font>

如果想看 heroku 上的資料，執行
`heroku pg:psql`
就會進到 psql 的 shell 中

`\list`
這個指令能印出 post gres 中的所有資料庫
不過如果在 heroku postgres 上執行的話，他只會跳出一堆以亂數取名的資料庫，對我們來說其實沒什麼意義
但如果在本地端開 psql 的話，你看到的就會是你之前創立過的資料庫了
要切換所在資料庫，執行 `\c <資料庫名稱>`

`\dt`
這個指令會印出目前所在的資料庫中有哪些 table

接下來你就能用 SQL 來獲取資料
假設資料表名稱是 table_issues
`SELECT * FROM table_issues`

## <font color="#f4a261">結語</font>

這是我第一次架設 api 
回頭一看發現學了好多東西
Flask, Flask-SQLAlchemy, Heroku, PostgreSQL
完全沒料到，原本以為只要學 Flask 就好 XD

不過也好啦，直接學了整個體系，包含api功能，架設到雲端，資料庫的存取
要不是我已經有了想達成的目標(優化TAN網站)
不然如果我只是單純想學做 API 的話可能只會學到 Flask 而已
之後的某月某日還是會需要惡補其他環節的
