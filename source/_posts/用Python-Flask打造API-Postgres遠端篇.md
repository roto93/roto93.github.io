---
title: 用Python-Flask打造API-Postgres遠端篇
date: 2021-07-30 16:46:57
tags:
---

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

