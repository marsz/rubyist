---
layout: post
title: "server 端允許 ajax cross domain 存取"
date: 2013-04-28 13:07
comments: true
categories: [ "API" ]
---

在 controller 中加入以下即可允許來自 cross domain 的 ajax request

```ruby
response.headers["Access-Control-Allow-Origin"] = "*"
```