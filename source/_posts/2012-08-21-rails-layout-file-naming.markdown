---
layout: post
title: "layout 檔案的命名"
date: 2012-08-21 15:16
comments: true
categories: ["Rails Bug"]
---
今天發現 `layout` 檔 (views/layouts) 的命名是不能亂來的 ~XD  
嘗試用 dash 命名 (例如 foo-bar.html.haml) 的結果造成 haml 無法被 parsing  
所以還是乖一點用小寫英文和 underline 吧 :p
