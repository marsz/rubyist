---
layout: post
title: "自動隱藏 iPhone safari 的網址列"
date: 2012-09-09 13:46
comments: true
categories: [HTML, Safari, iPhone]
---

在 `html` 的 `<head>` 裡面加上  

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0" />
<meta name="apple-mobile-web-app-capable" content="yes" />
<meta name="apple-mobile-web-app-status-bar-style" content="black" />
```
  
參考來源: <a href="http://stackoverflow.com/questions/4759818/how-to-hide-the-address-bar-of-a-webpage-in-android" target="_blank">http://stackoverflow.com/questions/4759818/how-to-hide-the-address-bar-of-a-webpage-in-android</a>
