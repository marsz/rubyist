---
layout: post
title: "Rails Rspec (1) 介紹與設定"
date: 2012-02-01 14:00
comments: true
author: MarsZ
categories: ["Rspec", "Rubygems", "Testing"]
---
有關寫測試的重要性, 本文不再贅述  
觀念性文章可參考以下大大們的見解...  
<!-- more -->

<a href="http://mrjamie.cc/2011/09/01/test-code/" target="_blank">Coding: 寫 Test 還是不寫 Test?</a> - Mr. Jamie  
<a href="http://ihower.tw/rails3/testing.html" target="_blank">Rails 3 實戰聖經: 測試 Testing</a> - ihower  
<a href="http://www.slideshare.net/ihower/rspec-7394497?from=ss_embed">Rspec 讓你愛上寫測試</a> - ihower

##### 安裝

參考官方文件: <a href="https://github.com/rspec/rspec-rails" target="_blank">https://github.com/rspec/rspec-rails</a>  

Gemfile  

```ruby
group :test, :development do
  gem "rspec"
  gem "rspec-rails"
  gem "factory_girl_rails" # 用於產生資料, 後面的文章將會介紹
  gem "shoulda-matchers" # 用於簡化寫 rspec 的工具, 後面的文章將會介紹
end
```

```
bundle install
```

產生檔案

```
rails generate rspec:install
```

移除舊的 test, 可視需求執行, 一般有了 rspec 就不會想再用舊的 test 框架啦 XD  

```
rm -rf test/
```

此時專案下會多了 spec 目錄, 以及 spec/spec_helper.rb  
另外透過 rails g 產生 model 或 controller 時, spec 目錄下也會產生對應的 spec 檔  
如

```
rails g model foo

```

```
      invoke  active_record
      create    db/migrate/20120204063026_create_foos.rb
      create    app/models/foo.rb
      invoke    rspec
      create      spec/models/foo_spec.rb
```

同理, rails destroy 也會對應刪除  

下一篇將會介紹基本語法以及跑測試的方法
