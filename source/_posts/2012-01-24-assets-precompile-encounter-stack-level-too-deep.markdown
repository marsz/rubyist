---
layout: post
title: "rake assets:precompile 時出現 stack level too deep 的訊息"
date: 2012-01-24 19:53
comments: true
categories: ["assets","assets-precompile"]
---
上禮拜終於要把服務佈署上去了  
結果在執行 precompile 時遇到錯誤訊息如下:
<!-- more -->

```
rake aborted!
stack level too deep
```

經過一陣搜索後發現, assets 檔案在同層目錄中, 不能有檔名和目錄名相同的情形  
例如

```
app/assets/admin/foo.scss
app/assets/admin.scss
```

主要是因為我的 admin.scss 是用來作 layout file 的
其內容

```css
/*
 *= require_tree ./admin
*/
```

於是在 precompile 時就發生了悲劇 :p  
後來將檔名更換後就沒問題了
