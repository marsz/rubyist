---
layout: post
title: "omniauth 登入後的 redirect 設定"
date: 2012-08-12 14:08
comments: true
categories: [Rubygems, Omniauth] 
---
我們曾在 <a href="/blog/2012-01-10/callbacks-of-devise-after-sign-in-or-sign-out/">Devise callbacks</a> 中，描述如何在登入後自定 redirect。這次則是在 omniauth 進行 open id 驗證時就先把 redirect 當參數先丟。

<!-- more -->

參考 <a href="https://github.com/intridea/omniauth/issues/218">官方作法</a>  
利用 `origin` 參數即可在 open id 驗證後進行 redirect

```ruby config/routes.rb
match '/auth/facebook', :as => :facebook_login
```

```ruby app/views/xxx.erb
# view 或 controller 下
facebook_login_path(:origin => "/url/for/redirect")
# 即 "/auth/facebook?origin=/url/for/redirect"
```

若 `devise` 中已經透過 application_controller.rb 的 `after_sign_in_path_for` 處理登入後的 redirect 時，則可以透過 `request.env['omniauth.origin']` 取得 origin 參數的內容。

```ruby app/controllers/application_controller.rb
def after_sign_in_path_for(user)
  return request.env['omniauth.origin'] || root_path
end
```

