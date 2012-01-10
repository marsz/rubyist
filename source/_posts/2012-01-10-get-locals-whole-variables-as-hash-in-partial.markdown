---
layout: post
title: "在partial中取得完整的locals(當Hash用)"
date: 2012-01-10 22:50
comments: true
categories: ["views-partial"]
---
我們都知道在一般的 view 下,即使在 controller 沒有指定 @foo 的值, view 直接讀取 @foo 也不會噴 Exception. 可是同樣的情況到了 partial 就會噴 Exception

<!-- more -->

render_partial 沒有只指定該變數的key到locals中

```erb app/views/bar/index.erb
<%= render_partial :foo, :locals => { :id => 1 }
```

在 partial 中直接讀取變數會噴 Exception

```erb app/views/bar/_foo.erb
<% if !foo %>
  not assign foo to locals
<% end %>
```

透過 local_assigns 便可將整個 locals 當 hash 用  

```erb app/views/bar/_foo.erb
<% if !local_assigns[:foo] %>
  not assign foo to locals
<% end %>
```

haml 版

```haml app/views/bar/_foo.haml
- if !local_assigns[:foo]
  = "not assign foo to locals"
```

