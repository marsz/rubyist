---
layout: post
title: "Url helper 中自定 host"
date: 2012-03-02 10:39
comments: true
author: MarsZ
categories: ["Url-helper"]
---
想要吐出 full url 時, 會用 `xxx_url`, rails 會抓 request 中的 host 做為 url 的 host, 但是當執行環境並非透過 http request (例如 background job), host 得自行指定  

<!-- more -->

rails 的 url helper 有提供參數 `:host` 可自行指定

```ruby
users_url :host => "foo.com"
```

若想要設定為預設, 可在 controller 中 override `default_url_options`

```ruby
class ApplicationController < ActionController::Base
  def default_url_options(options)
    {:host => "ohmygod.com"}
  end
end
```
