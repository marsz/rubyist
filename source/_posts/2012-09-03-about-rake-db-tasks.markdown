---
layout: post
title: "rake db 的 tasks 整理"
date: 2012-09-03 15:38
comments: true
categories: [Rake, Rubygems]
---

善用 rails rake 內建的 DB tasks 進行資料庫操作

<!-- more -->

`rake db:migrate` - 執行 db/migrate 中還沒跑過的 migrations  
`rake db:migrate VERSION=12341234` - 針對設定的 migrations 版本執行  
`rake db:migrate:redo` - 重跑目前最新的 migration  
`rake db:migrate:redo STEP=3` - 重跑目前最新的三個 migration  
`rake db:rollback` - 還原跑過最新的 migration  
`rake db:rollback STEP=3` - 還原跑過最新三個的 migration  
`rake db:create` - 建立 database  
`rake db:drop` - 刪除整個 database  
`rake db:schema:dump` - 從目前 database 中實際的 schema 建立 db/schema.rb  
`rake db:schema:load` - 從 db/schema.rb 中把 schema 建立到 databse 中  
`rake db:structure:dump` - 從目前 database 中實際的 schema 輸出到 db/structure.sql  
`rake db:reset` - 合體的 task ( db:drop => db:create => db:schema:load)

另外安裝了 <a href="https://github.com/ludicast/yaml_db" target="_blank">`yaml_db`</a> 後，可以透過以下兩個指令把資料庫的資料做 export/import (不包含 schema)  

`rake db:data:dump` - 把資料輸出到 db/data.yml  
`rake db:data:load` - 把資料從 db/data.yml 輸入到 database 中  

