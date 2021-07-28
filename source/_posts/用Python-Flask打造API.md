---
title: 用Python Flask打造API
date: 2021-07-27 15:42:32
tags:
---

<font size="2" color="#aaa">

## <font color="#f4a261">前言</font>

雖然我是覺得在學好一件事情之前不要亂學其他東西另闢戰場
但現實就是永遠有學不完的東西
也永遠沒有學好一件事情的時候
誰知道我會突然又需要學起 python 呢

事情是這樣
最近稍微熟悉一下 RWD 和 React 之後
我就開始動工 react 版本的 TAN 了
做著做著我便開始思考要怎麼簡化網頁的更新流程

目前的做法是每次收到老師的郵件，我就要手動進到伺服器去改檔案
雖然熟了操作起來還蠻快的，但還是有些麻煩而且有可能會東漏西漏
原本更慘，在我剛接手這個網站的時候
除了要打開每一份 html 檔更改標籤
還要手動做篩選，必須自己決定每個 issue 是被歸類在什麼類別
是後來我改用 javascript 選擇性 render html tag 
才讓更新網站的速度變得比較能夠接受
但終究我還是需要 ssh 進到伺服器然後把檔案傳上去

所以現在，我打算再更簡化一些
開發一個 api 架在雲端
只要進行 POST request 就能直接更新檔案
然後 react 端就只要 GET 接著渲染出來就好

#### 這代表我要學做 api 啦!!!

## <font color="#f4a261">該選什麼框架?</font>

一開始我就遇到了二選一
記得之前hahow的老師建議我可以用 python 寫 api
所以我查了一下有一個適合的模組 Flask
但後來我又發現有一個 Express.js
可以讓我用 javascript 寫 api 
這讓我有點猶豫 (畢竟我現在 JS 比 python 熟很多)

但最後我選了 flask 
正是因為 python 已經漸漸被我淡忘了
如果再不找機會練習的話，真的就要變回一張白紙了
那麼以下就是我建立第一個 flask API 的心路歷程

我會把步驟拆成:

1. 前置作業
2. 撰寫 API
3. 連接 database

## <font color="#f4a261">前置作業</font>

建立專案資料夾並進入

```
mkdir my-first-flask-api
```

這次決定自建API，也終於讓我知道虛擬環境的概念
基本上它就像是 node 專案裡的 node_module 資料夾
存放一些專案的 dependency
只不過跟node不同的是
node每個專案都有獨立的環境，所以不同專案間的依賴是互不影響的
但 python 就比較複雜，如果直接 pip 安裝任何模組
他們會被裝在 `...python/lib/site-packages...` 之類的地方
有點像 global install 的感覺
但其實沒那麼簡單，因為 python 又可以同時存在好幾個版本
結果就是各個專案間互相牽制，造成一堆問題

所以我們先來建置虛擬環境!
在眾多模組當中我選了 `virtualenv` 這個模組

首先 pip 安裝

`pip install virtualenv`

對於什麼時候要用 pip 安裝，什麼時候要用 pip3，之後我再寫一篇短文紀錄好了
大部分時候先用 pip 是 ok 的
有問題時再試 pip3

安裝好後執行以下指令，以在專案資料夾中建立一個虛擬環境資料夾

`virtualenv .venv`

他會根據你的作業系統
建立不同結構的.venv資料夾

### 啟用虛擬環境

#### Windows
如果用 cmd
`.\.venv\Scripts\activate.bat`

如果用 powershell (VScode是用這個)
`.\.venv\Scripts\activate.ps1`
或
`. .\.venv\Scripts\activate`

#### MacOS
`source ./.venv/bin/activate`
或
`. ./.venv/bin/activate`

執行完如果在命令列的最開頭看到 `(.venv)`
就代表成功了

接下來安裝本次最重要的模組 Flask

`pip install flask`

完成後可以輸入 `pip show flask`
檢查模組是否如期被安裝在 .venv 虛擬環境中
如果在這之前沒有先啟用虛擬環境的話
flask 就會被安裝在 site-package 那裏

安裝完所需的模組後執行:
`pip freeze > requirements.txt`

`pip freeze` 有點像為目前的開發環境拍張 snapshot
紀錄所使用的模組有哪些
就好像凍結這一瞬間一樣(莫名有點浪漫?)
後面的 `>` 代表要把左側的輸出值寫入右側的檔案
也就是 `requirements.txt` 中

這是為了讓其他人 clone 這份專案後能夠以
`pip install -r requirements.txt` (-r: --requirement)
安裝專案所需的模組

這邊要注意，最好是使用複數的 requirements
雖然打什麼檔名都可以 `pip install -r`
但有些時候會嚴格檢查(如Heroku)
所以還是要照規矩來比較好

## <font color="#f4a261">撰寫API</font>

我們建立一個 python file `app.py`

```python
from flask import Flask

app = Flask(__name__)

if __name__ == "__main__":   # 當這份程式被作為主程式執行時
    app.run(debug=Ture)   # 執行app，debug=True會讓app處於監聽狀態
```

接著我們建立根路徑

```python
@app.route('/') # Decorator。 在這個路徑下接收到 request (預設為GET) 的話要做以下的事
def index():
    return "Hello world from Flask"
```

目前整個程式非常單純
就是引入flask後啟動它
並設定當根路徑獲得GET請求時要回傳什麼資訊

接下來我們回到 terminal
執行 `python app.py`
便能開啟API並等待請求

```
FSADeprecationWarning: SQLALCHEMY_TRACK_MODIFICATIONS adds significant overhead and will be disabled by default in the future.  Set it to True or False to suppress this warning.
  warnings.warn(FSADeprecationWarning(
 * Debugger is active!
 * Debugger PIN: 124-333-105
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

打開瀏覽器並進到 http://127.0.0.1:5000/
就會看到輸出的字串了!

## <font color="#f4a261">待續</font>


文章先寫到這吧不想讓這篇變太長
串接資料庫的部分就留到下一篇囉
到時也會把 API 的 POST, DELETE 方法給做出來

</font>