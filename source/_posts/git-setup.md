---
title: Git 快速上手
date: 2021-03-30 23:39:27
tags:
---
 <font size="2" color="#aaa">

 這篇文章會教你如何建立 repository，以及如何上傳到 github	

 1. 安裝 [Git](https://git-scm.com/downloads 'Git官網')

 2. 開啟終端機，進到你想建立 local repository 的資料夾，接著
 ```
 git init
 ```
 這時候就會建立一個隱藏資料夾 .git/ ，我們就能在這個資料夾內使用 git 的 command line 了

 3. 接下來到 [github](https://github.com 'Github官網') 註冊帳號並建立 remote repository
<img src="github_create_repository.png" width="600px" />


 1. 到 repository 的首頁，點擊 code 按鈕後複製 HTTPS 網址

<img src="github_get_URL.png" width="600px" />

 1. 回到 local repo，現在我們需要把本地和遠端的 repo 連結起來

 ```
 git remote add origin 剛剛的URL
 ```

	這個 origin 是我們幫 URL 取的名字，可以改成任何名稱

 6. 接下來我們可以在 local 做一些更改，例如編輯 README.md 
 	然後就能上傳到 remote 了

 	```
 	git add .
 	``` 

 	(將這個資料夾內的變動加入到暫存區中)

 	```
 	git commit -m ''
 	```

 	(確定變更，-m指的是提交暫存區的檔案，引號內可輸入註解，讓別人或自己知道理由或變動的檔案有哪些)

 	最後，將變更 push 到 remote:

 	```
 	git push -u origin master
 	```

 	這裡指的是要把commit過的檔案 push 到 origin 這個網址上的 master，-u是儲存設定，這樣下次你只需要

 	```
	git push
 	```

 	他就會幫你上傳到 origin 的 master 上了。

 	至於這個 master，我們可以把 repository 視為一棵樹，這棵樹一般來說就是沿著樹幹(master)的方向增長，但到了某個時機我們可能需要他分支(branch)出來，做別的事情

 	code 也是一樣的，有時候我們需要開發新的功能或版本，我們就需要 branch ，這樣在我們開發的過程中，不會去影響 master 上的開發，等到你開發到某個階段，就可以 merge，把 master 和 branch 合併起來。

 	That's it.

 	如果有什麼需要更正的，歡迎聯繫我：roto93@yahoo.com.tw