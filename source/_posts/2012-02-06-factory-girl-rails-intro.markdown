---
layout: post
title: "使用 Factory Girl 產生測試用資料"
date: 2012-02-06 16:57
comments: true
author: MarsZ
categories: ["Rubygems", "Factory-girl", "Rspec"]
---
在寫測試的時候, 為了實際測試程式處理資料的正確性  
免不了要依據資料庫的關聯建立假資料  
但往往會因為 model 增加了一些 validation 而導致產生假資料的 code, 必須符合驗證而修改  
當測試的量日漸龐大時, 改起來就會非常麻煩  
<!-- more -->

factory_girl_rails 讓產生假資料的方式變得簡單和一致, 同時提昇 code 的可讀性  
例如:  

```ruby
before do
  @user = User.create(:name => "foo", :email => "foo@bar.com", :state => :block)
end
```

改用 factory_girl 後

```ruby
before do
  @user = Factory :user_state_blocked
end
```

#### 安裝

```ruby Gemfile
group :test, :development do
  gem "factory_girl_rails"
end
```

```ruby
bundle install
```

#### 設定

factory 的設定建議一個檔案對應一個 model, 放在 spec/factories 下  
例如 app/models/user.rb => sepc/factiries/user.rb  
為了讓 rails g model 的時候, 可以自動產生對應的 factory file, 可以加入以下 code 在 config/application.rb  

```ruby config/application.rb
config.generators do |g|
  g.test_framework :rspec, :fixture => true, :views => false, :fixture_replacement => :factory_girl
  g.fixture_replacement :factory_girl, :dir => "spec/factories" 
end
```

之後只要 "rails g model user" 也會自動產生 spec/factories/user.rb  
內容如下  

```ruby spec/factories/user.rb
FactoryGirl.define do
  factory :user do
  end
end
```

針對已經存在的 model 可以透過 -s 來略過已存在的檔案

```ruby
rails g model user -s
```

#### 定義與使用

基本, factory 內的 method 對應 column name

```ruby spec/factories/user.rb
FactoryGirl.define do
  factory :user do
    name "foo"
    email "bar@foo.com"
    password "12341234"
    birthday "1982-03-30"
  end
end
```

巢狀定義, 可用於某種 test case 下的資料內容, 下層的 column 內容會覆寫上層  

```ruby spec/factories/user.rb
FactoryGirl.define do
  factory :user do
    name "foo"
    email "bar@foo.com"
    password "12341234"
    birthday "1982-03-30"
    state "actived"
    factory :user_state_locked do
      state "locked"
      locked_at "2012-12-12"
    end
    factory :user_state_closed do
      state "closed"
      closed_at "2012-12-12 12:12:12"
    end
  end
end
```

```ruby spec/models/user_spec.rb
@user = Factory :user
@user_state_closed = Factory :user_state_closed
@user_state_locked = Factory :user_state_locked
```

使用 factory 時, 也可以在後面的參數帶入自定欄位內容或關連  

```ruby spec/models/user_spec.rb
@user = Factory :user, :birthday => "1983-01-28"
@post = Factory :post, :user => @user
```

下一篇將介紹更進階的 factory 定義!!
