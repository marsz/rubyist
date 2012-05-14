---
layout: post
title: "輸出除去0的日期格式"
date: 2012-01-10 22:34
comments: true
author: MarsZ
categories: ["Date", "String"]
---
ruby 中 Date 或 Datetime 都有 <a href="http://ruby-doc.org/stdlib-1.9.3/libdoc/date/rdoc/Date.html#method-i-strftime" taget="_blank">#srtftime</a> 可以用來作自定格式的日期輸出  
若我們希望輸出的日期或時間能夠去除多餘的0, 或者輸入的月份英文可以全部大寫  
以下範例 (Date, Datetime均適用) 擷取自官方文件可作為參考  

<!-- more -->

```ruby
date = "2012-01-01".to_date
date.strftime("%m/%d") # 01/01
date.strftime("%-m/%-d") # 1/1
date.strftime("%d %b") # 1 Jan
date.strftime("%d %^b") # 1 JAN
```

完整細節可以參考<a href="http://ruby-doc.org/stdlib-1.9.3/libdoc/date/rdoc/Date.html#method-i-strftime" target="_blank">官方文件</a>
