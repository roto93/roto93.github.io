---
title: 不建議在 Heroku 上用 SQLite 當 DB?
date: 2021-07-28 23:40:07
tags:
---

## <font color="#f4a261">前言+中言+後言</font>

這會是個小短篇
在前兩篇文章中我介紹了怎麼用 python flask 架設 api 
串接 sqlite 資料庫後放到 Heroku 上運作

後來我想到個問題
就是api如果經過POST請求修改了資料庫
那在雲端更新的資料要怎麼抓下來到本地中?

我試了 `git pull heroku main`

但它卻告訴我 Already up to date.

[Heroku Dev Center的一篇文章](https://devcenter.heroku.com/articles/sqlite3) 有提到這件事
他說 sqlite 雖然方便又適合新手，但不建議用在 production

> SQLite runs in memory, and backs up its data store in files on disk. While this strategy works well for development, Heroku’s Cedar stack has an ephemeral filesystem. You can write to it, and you can read from it, but the contents will be cleared periodically. If you were to use SQLite on Heroku, you would lose your entire database at least once every 24 hours.

意思是 SQLite 在記憶體運作，每隔一段時間 Heroku 就會把它清除

> Even if Heroku’s disks were persistent running SQLite would still not be a good fit. Since SQLite does not run as a service, each dyno would run a separate running copy. Each of these copies need their own disk backed store. This would mean that each dyno powering your app would have a different set of data since the disks are not synchronized.

老實說這一段我不懂，但看到一些關鍵字說會發生每個 dyno (這啥)都有不同的 data set 的情形發生

所以他們推薦使用 Postgresql

那我就學這個吧我想應該就一些前置作業的不同吧
畢竟都是要用 ORM 來操作