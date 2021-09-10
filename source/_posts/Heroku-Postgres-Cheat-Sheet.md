---
title: Heroku Postgres Cheat Sheet
date: 2021-09-07 11:08:51
tags:
---

<font size="3" color="#aaa">

## <font color="#f4a261">前言</font>

這裡記錄一些 Heroku Postgres 常用的指令

由於我不算主攻資料庫，這些指令頂多在建立API時才會用到
真的很容易忘記
為自己做點紀錄，不用每次都去翻 doc
這篇文章會持續更新

---

## SQL 語法

```sql
-- 新建資料庫
CREATE DATABASE name;

-- 更改資料庫名稱
ALTER DATABASE <name> RENAME TO <newname>


-- 刪除整個資料表
DROP TABLE table_name; 

-- 刪除資料表中的內容，但保留資料表
TRUNCATE TABLE table_name;

-- 刪除整個資料庫
DROP DATABASE database_name;

--顯示某 table 的所有欄位
SELECT column_name 
 FROM information_schema.columns 
 WHERE table_name = 'table_issues' 
 AND table_schema = 'public';

```

## 遠端 heroku postgres 操作

- 查看現有備份檔
`heroku pg:backups`

- 立刻建立備份
  (指定app) `heroku pg:backups:capture --app taiwan-astronomy-network-api`
  (指定database) `heroku pg:backups:capture table_issues`

- 排程備份
`heroku pg:backups:schedule --at "06:00 Asia/Taipei"`
flag --at 是必需參數，格式為: "<font color="orange">hour</font>:00 <font color="orange">full TZ format</font>"
Windows 必須使用雙引號
Full TZ format 查詢: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones

- 查看排程
`heroku pg:backups:schedules`

- 取消排程
`heroku pg:backups:unschedule DATABASE_URL`
DATABASE_URL: 如果一個 APP 有多個 DATABASE，則需指定 DATABASE 的 URL

- 下載備份
`heroku pg:backups:download BACKUP_ID -o OUTPUT_NAME`
BACKUP_ID: 要下載的備份檔id，可先用 heroku pg:backups 查詢 
OUTPUT_NAME: 輸出的路徑與檔名，預設為 `latest.dump`，但如果沒有設定的話不會覆寫同名檔案，而是變成`latest.demp.1`

## 本地 postgres 操作

- 用 latest.dump 復原本地資料庫
`pg_restore --verbose --clean --no-acl --no-owner -h localhost -U postgres -d tan_data latest.dump`



