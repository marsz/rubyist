---
layout: post
title: "在 Capistrano 中 deploy 時能夠自動執行 rake assets:precompile"
date: 2011-12-25 15:14
comments: true
author: MarsZ
categories: ["Rubygems", "Capistrano", "Deploy", "Assets"] 
---

當我們在專案目錄中執行 "capify ." 時, 會產生 Capfile 檔
在該檔案加上

```ruby Capfile
load 'deploy/assets'
```
<!-- more -->

capistrano 會將 current/public/assets 連結到 shared/assets 中
若 shared 下無此目錄, 記得要 mkdir 一下
