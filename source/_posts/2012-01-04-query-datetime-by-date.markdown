---
layout: post
title: "如何在 datetime 的欄位中做日期的搜尋"
date: 2012-01-04 16:54
comments: true
categories: ["Sql", "ActiveRecord"]
---

rails 在儲存 datetime 時,會先去除時區,然後才把 +0 的原始時間存到資料庫中
因此若我們直接下 sql 時, 必須將時區考量進去

<!-- more -->

例如我們想要找欄位 created_at 在 2012-01-01 00:00:00 到 2012-01-03 23:59:59 的資料
以台灣 +8 時區為例, 我們應該找資料庫中 2011-12-31 16:00:00 到 2012-01-03 15:59:59 的資料
以下範例可直接達到所想要的 sql

```ruby
date_start = "2012-01-01"
date_end = "2012-01-03"
range = date_start.to_date.beginning_of_day..date_end.to_date.end_of_day
User.where(:created_at => range)
```

<a href="http://api.rubyonrails.org/classes/Date.html" target="_blank">Date</a>中有 "beginning_of_day" 和 "end_of_day" 兩個 method 將 Date 轉換為 Datetime 並且使時間落在當天的開始與結束, 透過 ActiveRecord 以 hash 方式作為 query interface, 時區才會自動轉換

同樣在 Date 中還有 beginning_of_month, beginning_of_quarter, beginning_of_week, beginning_of_year 可用

###### 補充

有關時區設定可參考 config/application.rb

