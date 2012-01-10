---
layout: post
title: "Encoding::CompatibilityError: incompatible character encodings: ASCII-8BIT and UTF-8"
date: 2011-12-23 21:01
comments: true
author: MarsZ
categories: [Encoding]
---

在進行 regular expression 或其他字串處理時, 出現以下錯誤訊息

```
Encoding::CompatibilityError: incompatible character encodings: ASCII-8BIT and UTF-8
```

<!-- more -->

###### 來源

<a href="https://rails.lighthouseapp.com/projects/8994/tickets/4336-ruby19-submitted-string-form-parameters-with-non-ascii-characters-cause-encoding-errors" target="_blank">https://rails.lighthouseapp.com/projects/8994/tickets/4336-ruby19-submitted-string-form-parameters-with-non-ascii-characters-cause-encoding-errors</a>

###### 解決

```ruby
mystring.force_encoding("UTF-8")
```

總之就是把所有的字串都 force_encoding :p
