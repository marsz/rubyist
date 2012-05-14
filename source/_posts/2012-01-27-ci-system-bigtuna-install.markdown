---
layout: post
title: "CI server - Bigtuna (安裝篇)"
date: 2012-01-27 11:17
comments: true
author: MarsZ
categories: ["Continuous Integration", "Bigtuna"]
---
CI 全名 "Continuous Interation"  
用於開發流程中的一種管理技巧  
在每一次的修改後, 能夠自動化進行相關測試, 讓該次修改正確無誤的整合進現行系統中  
相關介紹可參考 ihower 的 <a href="http://ihower.tw/rails3/testing.html" target="_blank">測試 Testing</a>  
今天要介紹的則是用於 CI system 的 Bigtuna  
<!-- more -->

##### 參考官方文件

<a href="http://bigtuna.appelier.com/" target="_blank">http://bigtuna.appelier.com/</a>  

##### 安裝步驟

從 github clone  

```
git clone git://github.com/appelier/bigtuna.git
cd bigtuna
```

設定 Gemfile

```
vim Gemfile
```

2012.1.27 筆記: 因為 bigtuna 目前採用 rails 3.0.3, 而現行 rails 最新版為 3.1.2, 有許多 gem 因應更新, 而導致 3.0.3 無法使用最新版的 gem, 因此下列設定是針對 3.0.3 而做的版本修正, 若 bigtuna 有更新 rails 版本, 則下列的範例可能會不適用  

```ruby
gem "mysql2", "0.2.6" 
gem "delayed_job", "~> 2.1.4"
```

設定 config/database.yml 以 mysql 為例, 僅供參考

```ruby config/database.yml
production:
  adapter: mysql2
  encoding: utf8
  reconnect: false
  database: bigtuna_production
  pool: 5
  username: "db_user" 
  password: "db_password" 
  host: "db_host" 
```

安裝

```
bundle install
bundle exec rake db:create RAILS_ENV=production
bundle exec rake db:schema:load RAILS_ENV=production
```

啟動 delayed job  

```
RAILS_ENV=production ./script/delayed_job start
```

完成!!!  
透過 web server 連線後, 看到以下畫面就表示 ok 啦!!  
<img src="https://img.skitch.com/20120204-dayqp1qsffq3cnei1fgcpi3e2d.png" width="700" />  
下一篇將介紹如何設定專案  

