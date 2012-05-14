---
layout: post
title: "Devise 在登入或登出之後執行回乎"
date: 2012-01-10 23:11
comments: true
author: MarsZ
categories: ["Rubygems","Devise"]
---
#### 需求

想要在登入或登出後執行指定的行為
<!-- more -->

#### 官方 wiki

<a href="https://github.com/plataformatec/devise/wiki/How-To:-Change-the-redirect-path-after-destroying-a-session-i.e.-signing-out" target="_blank">How To: Change the redirect path after destroying a session i.e. signing out</a>  

#### 說明

* 從 <a href="https://github.com/plataformatec/devise/blob/master/app/controllers/devise/sessions_controller.rb" target="_blank">SessionsController</a> 可得知每次登入後會 redirect 到 after_sign_in_path_for 這個 helper method 所回傳的路徑, 登出則是 after_sign_out_path_for  
* 找到 <a href="https://github.com/plataformatec/devise/blob/master/lib/devise/controllers/helpers.rb#L207" target="_blank">Controller::Helper</a> 定義了  after_sign_in_path_for 和 after_sign_out_path_for  
* 針對這兩個 method 進行覆寫

#### 範例

注意要用 private, 並且傳進一參數為 model instance
```ruby app/controllers/application_controller.rb
class ApplicationController < ActionController::Base
  private

  # Overwriting the sign_in redirect path method
  def after_sign_in_path_for(resource_or_scope)
    root_path
  end
end
```
