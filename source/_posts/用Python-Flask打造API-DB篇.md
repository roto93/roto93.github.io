---
title: 用 Python Flask 打造 API - DB 篇
date: 2021-07-28 09:55:13
tags:
---

<font size="2" color="#aaa">


## <font color="#f4a261">前言</font>

繼前一篇用 Flask 建立簡單的 API
我把大部分篇幅用來介紹我為什麼要做，為什麼選flask的心路歷程
以及前置作業上
這篇則會把來不及提到的資料庫環節記錄下來

先回顧一下前一篇的程式樣貌
僅定義了啟動 app 和根路徑的 GET 方法

```python
from flask import Flask

app = Flask(__name__)

@app.route('/') # Decorator。 在這個路徑下接收到 request (預設為GET) 的話要做以下的事
def index():
    return "Hello world from Flask"

if __name__ == "__main__":   # 當這份程式被作為主程式執行時
    app.run(debug=Ture)   # 執行app，debug=True會讓app處於監聽狀態
```

## <font color="#f4a261">建立 sqlite database</font>
為了在 flask 中操作資料庫
我們需要使用 flask_sqlalchemy 模組
`pip install flask_sqlalchemy`
接著在 app.py 中
`from flask_sqlalchemy import SQLAlchemy`

並且我們會使用 ORM 的技術來操作資料庫 (這裡我們選擇輕量的資料庫:sqlite)

ORM 是 Object Relational Mapping(物件關聯對映)
一句話解釋就是

> 用操作物件的方式操作資料庫

這樣即使我們不用 SQL 語法，也可以使用 SQL 資料庫了
開始建立資料庫!

```python
# 設定要連線的資料庫
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///data.db'
db = SQLAlchemy(app) # 宣告 db
```

`'SQLALCHEMY_DATABASE_URI'`這個參數是告訴app你要跟什麼資料庫連接
以我們的例子是用 sqlite，所以設為`sqlite:///data.db`
注意有三個斜線 它的意思是相對路經
後面的`data.db`是你待會要創建的 db 的檔案名稱

如同剛剛說的 ORM 是以操作物件的方式進行
我們需要新增一個 class 作為資料庫的 model

```python
class Issue(db.model): # 宣告一個類別，這個類別繼承 db.Model
    id = db.Column(db.Integer, primary_key=True) # 宣告一個id欄位，型別為db.Integer，
    name = db.Column(db.String(80), unique=True, nullable=False) # 宣告一個name欄位
    content = db.Column(db.String(20000)) # 宣告一個 content 欄位

    def __repr__(self):
        return f"{self.name} - {self.content}"
```

接下來我們回到 terminal 輸入 `python`
以打開 python 的 interactive prompt

假如說我想在 API 中存放 TAN 網站的 email 文字檔
那我就把每一份文字檔都叫作 issue 好了
逐一輸入以下代碼

```python
from app import db  # 引入db模組
db.create_all() # 建立db，會生成一個 data.db 檔案
issue = Issue(name='issue 1', content="This is issue 1.") # 建立第一筆資料
db.session.add(issue) # 把資料加入資料庫
db.session.commit() # 確認，提交變更
```

就能在資料庫中建立第一份 issue
在下一章節，我們要把資料秀出來


## <font color="#f4a261">新增 endpoint</font>

一個 API 通常不會只有根目錄有作用
於是 endpoint 就派上用場了
endpoint 指的是 API 的對外接口
API的每個有效路徑都是一個 endpoint
那我就可以建立一個 endpoint 叫做 '/issues'
只要使用者對這個 endpoint 送出請求
API 就會回傳相對應的資料
具體來說怎麼做呢

```python
@app.route('/issues') # 新增一個端點
def get_issues():
    issues = Issue.query.all() # 回傳包含所有Issue物件的列表

    # 由於issues是一個列表
    # 但 api 的回傳值必須是 json serializable 所以不能是列表
    # 必須是字串或是 dictionary
    # 即使是 dictionary，裡面的所有 value 也都必須是 json serializale
    # 簡單來說整個回傳值必須要長得跟 json 結構一模一樣，否則就必須是 string
    output = []
    for issue in issues: 
        issue_data = {"name": issue.name, "content": issue.content}
        output.append(issue_data)
    return {"issues": output}
```
#### 補充說明:
`Issue.query.all()` 查了很久都不知道這個 query 是誰的語法
後來才知道他是`db.session.query(Issue)`的縮寫
一些相關的用法如下:

**取得所有 todo 資料**
`Todo.query.all()`

**限制 1 筆資料**
`Todo.query.limit(1).all()`

**正向/逆向排序**
`Todo.query.order_by(Todo.content).all()`
`Todo.query.order_by(Todo.content.desc()).all()`

**取得第一筆資料**
`Todo.query.first()`

**取得 primary key=1 一筆資料**
`Todo.query.get(1)`

如此一來
連接到 http://127.0.0.1:5000/issues 時就會看到 {"issues":[{"name":"issue 1", "content":"This is issue 1."}]}

## <font color="#f4a261">GET+parameter</font>

如果我們需要帶參數要怎麼寫呢?
那就要在路徑中用 `<` `>` 夾住參數
這樣就能在function中傳入參數了
如下:

```python
@app.route('/issues/<id>') # 把接在 /issues/ 後面的字令作 id
def get_issue(id): # 傳入 id
    # get_or_404() 跟 get() 類似，差別在於如果 id 不存在不會回傳 None 而是 404 error
    issue = Issue.query.get_or_404(id) 
    return {"name":issue.name,"content":issue.content}
```

這樣就可以獲取第一筆資料

補充說明:
`Issue.query.get_or_404(id)` 這邊之所以可以用 id 來獲取檔案
是因為前面在定義 id 的 Column 的時候，有特別設定 primary_key=True，意思是設為主鍵
SQLite 主鍵是用於定義唯一行記錄的一個值。一個表只能有一個主鍵。
其實它的功用就像 id 一樣，確保每筆資料都是唯一的不重複。


## <font color="#f4a261">撰寫 POST 方法</font>

想要新增 POST 方法，作法跟 GET 很像
只不過我們要指定 app.route 的 methods 參數

```python
@app.route('/issues', methods=["POST"]) # 設方法為 POST
def add_issue():
    # 根據 request 的 body 的內容建立新的 Issue 物件
    issue = Issue(name=request.json["name"], content=request.json["content"]) 
    db.session.add(issue) # 加入 db 
    db.session.commit() # 確認提交
    return {"id": drink.id} # 回傳 id
```

POST 有沒有成功比較難單用瀏覽器測試
所以我們可以用 postman 如下 (記得要選擇 POST 再按 send)

<img src="post-man-1.png" width="700px">

## <font color="#f4a261">撰寫 DELETE 方法</font>

最後是刪除
做法跟POST類似，都需要給予參數

```python
@app.route('/issues/<id>')
def delete_issue(id):
    drink = Drink.query.get_or_404(id) 
    db.session.delete(drink)
    db.session.commit()
    return {"message":f"Id {id} deleted!"}
```

## <font color="#f4a261">感想</font>


終於講完啦!!! 寫了好久天哪
寫這兩篇之前我還以為我會的差不多了
但一解釋起來才發現細節其實完全不會
一邊惡補一邊寫哈哈
這就是我想寫部落格的目的 讚

不過現在我只做了非常非常單純也不實用的 api
還需要很多加強，實際應用在網頁上也不知道會發生什麼事情
就看我未來幾天的造化囉

下一步就是要練習把 api 部屬到網路上了
我應該會用 Heroku 這個平台吧
之後再整理上來囉~

</font>