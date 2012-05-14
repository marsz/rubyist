---
layout: post
title: "用 resque 實作背景作業 (前置設定篇)"
date: 2012-02-13 14:51
comments: true
author: MarsZ
categories: ["Rubygems", "Resque", "Message-queue", "Background-job"]
---
resque 是一套可以用來實作 background job 的 gem  
舉凡長時間的執行如 email, 點數計算等等非即時性的工作, 都可以採用非同步執行  
以加速前端服務的反應速度  
resque 是以 redis 做為 message queue server 的方式來進行...

<!-- more -->

#### 參考連結  

RailsCast - 影片: <a href="http://railscasts.com/episodes/271-resque" target="_blank">Resque</a>  
RailsCast - 文字: <a href="http://railscasts.com/episodes/271-resque?view=asciicast" target="_blank">Resque</a>  
官方文件: <a href="https://github.com/defunkt/resque" target="_blank">Github</a>  

#### 安裝 Gem

```
# Gemfile
gem "resque"
```

```
bundle install
```

#### 安裝 redis

MacOS

```
sudo brew install redis
```

Debian / Ubuntu 

```
sudo apt-get install redis-server
```

#### 啟動/停止 redis server

MacOS

```
# 啟動
/usr/local/bin/redis-server /usr/local/etc/redis.conf
# 停止 ps + kill :p
sudo kill -SIGHUP $(ps aux|grep 'redis-server'|grep -v 'grep'|awk '{print $2}')
```

Debian / Ubuntu

```
# 啟動
sudo service redis-server start
# 停止
sudo service redis-server stop
```

#### Resque 中的 redis 設定

由於 resque 必須將 job 丟上 redis (mq server)  
因此必須設定連線的位址  

手動建立兩個檔案  
config/redis.yml => redis 的連線設定檔  
config/initializers/resque.rb => 初始化 resque  

```yml config/redis.yml
development: 
  :redis: "localhost:6379"
```

```ruby config/initializers/resque.rb
config = YAML.load_file("#{Rails.root}/config/resque.yml")[Rails.env]
Resque.redis = config[:redis]
```

###### RedisToGo

若是使用雲端的Redis服務-<a href="https://redistogo.com/" target="_blank">RedisToGo</a>  
可以直接在 yml 中貼上 instance 的 uri, 如

```yml config/redis.yml
development: 
  :redis: "redis://marsz:xxxxxx@stingfish.redistogo.com:9999"
```

###### redis-rb

若 redis 有同時兼作 cache 或 db 用, 而且是透過 gem "redis" 進行連線的話  
則可以直接將變數指定

```yml config/redis.yml
development: 
  :db: 0
  :host: "localhost"
  :port: 6379
```

```ruby config/initializers/resque.rb
config = YAML.load(File.open("#{Rails.root}/config/redis.yml"))[Rails.env]
Resque.redis = Redis.new( :host => config[:host], :port => config[:port], :db => config[:db] )
```

一般小弟比較偏好最後一個方法, 因為 redis 太方便了, 只做 background job 有點可惜 XD  

#### Resque server

想要有直觀的介面可以看目前 message queue (redis) 上的 job 執行狀況  
resque 也有提供 :)  

```ruby config/initializers/resque.rb
# 在任何 initial 的檔案中加都可
require "resque/server"
```

```ruby config/routes.rb
mount Resque::Server.new, :at => "/admin/resque"  
```

啟動 rails server, 連線 <a href="http://localhost:3000/admin/resque" target="_blank">http://localhost:3000/admin/resque</a> 就可以看到啦

#### 啟動 worker

丟上 message queue 的工作, 必須透過 worker 逐個要下來執行  
啟動worker...

```ruby
QUEUE=* bundle exec rake resque:work
```

上述指令啟動後就會掛著, 而 server 上的啟動可以透過 BACKGROUND=yes 讓 worker 變成背景執行, 之後會再介紹完整參數說明

worker 在 message queue 中有許多 issue, 例如:  
1. 佈署時如何自動的重啟  
2. server 上的 worker 掛了要怎麼自動重啟  
3. worker 能否 multi-thread 執行或針對特定 queue 等等  
將在後續會有相關 solution 的分享  

------------------------

以上是有關 resque 的環境設定  

redis => resuqe config => worker

下一篇將會介紹在 rails 中的基本使用方式
