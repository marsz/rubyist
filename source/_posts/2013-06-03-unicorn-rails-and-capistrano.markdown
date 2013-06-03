---
layout: post
title: "安裝與設定 unicorn 實現佈署時 zero down time"
date: 2013-06-03 11:27
comments: true
categories: ["Capistrano", "Rubygems"]
---

本文章的 code 可參考公開的 pull request  
<a href="https://github.com/5fpro/cupid/pull/11/files" target="_blank">https://github.com/5fpro/cupid/pull/11/files</a>  
這是基礎到不行的設定方式，考量到 server 效能在設定上的 best practice 將會在日後持續 update 本文。

<!-- more -->

### 安裝 gem  

```ruby Gemfile
group :development do
  gem 'capistrano-unicorn', :require => false
end

gem 'unicorn'
```

### 佈署設定

這裡的 `production.rb` 是針對 `rails_env=production`，而非 capistrano 中 multistage 的 production

```ruby config/unicorn/production.rb
worker_processes 1
preload_app true

listen '/tmp/unicorn.cupid.sock' # sock 檔名可依照 app 需求設定

stderr_path 'log/unicorn.error.log'
stdout_path 'log/unicorn.log'

pid "tmp/pids/unicorn.pid"

rails_env = ENV['RAILS_ENV'] || 'production'

# before/after fork 可自行擴充

before_fork do |server, worker|
end

after_fork do |server, worker|
end
```

(也可以把上述檔案放在 `config/unicorn.rb`)

### server 上的 nginx.conf

```ruby nginx.conf
upstream my_app {  # upstream 名稱可自定
  server unix:/tmp/unicorn.cupid.sock; # 這裡要和 config/unicorn/production.rb 中 listen 的設定對應
}

server {
  listen 80;
  server_name example.com;

  # passenger_enabled on; # 原本要設定 passenger 的記得要拿掉
  # .......

  location / {
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_pass http://my_app; # upstream 名稱
  }

  # .......
}
```

### 把 unicorn 重啟的 hook 掛到 deploy.rb 中  

```ruby config/deploy.rb
require 'capistrano-unicorn'
after 'deploy:restart', 'unicorn:restart'
```

done!  

### 如何在本機測試 unicorn

`bundle exec unicorn`

Go to http://localhost:8080


### 參考資料

* 更多 unicorn 可以在 cap 指令中的應用: <a href="https://github.com/sosedoff/capistrano-unicorn/blob/master/lib/capistrano-unicorn/capistrano_integration.rb" target="_blank">https://github.com/sosedoff/capistrano-unicorn/blob/master/lib/capistrano-unicorn/capistrano_integration.rb</a>  
* unicorn 設定檔說明: <a href="https://raw.github.com/defunkt/unicorn/master/examples/unicorn.conf.rb" target="_blank">https://raw.github.com/defunkt/unicorn/master/examples/unicorn.conf.rb</a>  
* Github Blog 上說明他們自己的 unicorn 如何設定: <a href="https://github.com/blog/517-unicorn" target="_blank">https://github.com/blog/517-unicorn</a>  
* 其他 unicorn 設定相關教學:
  * <a href="http://tech.gadii.net/blog/2013/03/07/rails-deploy-ji-chu-jiao-xue/" target="_blank">http://tech.gadii.net/blog/2013/03/07/rails-deploy-ji-chu-jiao-xue/</a>
  * <a href="https://devcenter.heroku.com/articles/rails-unicorn" target="_blank">https://devcenter.heroku.com/articles/rails-unicorn</a>
  * <a href="http://ruby-china.org/topics/471" target="_blank">http://ruby-china.org/topics/471</a>