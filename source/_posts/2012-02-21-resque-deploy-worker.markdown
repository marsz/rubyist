---
layout: post
title: "用 resque 實作背景作業 ( 佈署篇)"
date: 2012-02-21 01:56
comments: true
author: MarsZ
categories: ["Rubygems", "Resque", "Message-queue", "Background-job", "Deploy", "Capistrano"]
---
透過 capistrano 佈署到 server 時，除了需要啟動 worker 之外，也會希望在每次佈署後，也重新啟動 worker  
因為 worker 是透過 rake 啟動的，所以當 perform 相關的程式有修改時，worker 也必須重新啟動  
以下將介紹如何將重啟 worker 整合到 capistrano 中

<!-- more -->

#### 將 worker 丟背景執行的方法

resque 1.9 以上有提供參數 :)

```
BACKGROUND=yes QUEUE=foo_queue bundle exec rake environment resque:work
```

背景的 worker 要留一下 pid file, 以方便 kill

```
PIDFILE=./resque.pid BACKGROUND=yes QUEUE=foo_queue bundle exec rake environment resque:work
```

#### 整合 capistrano

deploy 中加 restart_resque 的 task

```ruby config/deploy.rb
namespace :deploy do
  # .....
  task :restart_resque, :roles => :app do
    queues = [:foo_queue, :barbar]
    queues.each do |queue|
      pid_file = "#{current_path}/tmp/pids/resque-#{queue}.pid"    
      run "test -f #{pid_file} && cd #{current_path} && kill -s QUIT `cat #{pid_file}` || rm -f #{pid_file}"
      run "cd #{current_path} && PIDFILE=#{pid_file} RAILS_ENV=#{rails_env} BACKGROUND=yes QUEUE=#{queue} bundle exec rake environment resque:work"
    end
  end
  #...
end
```

上面的例子是以一條 queue 配一個 worker 的方式執行，若要通通塞一個 worker 就 QUEUE=* 吧 XD  

restart app 後就 restart resque worker 吧!!

```ruby config/deploy.rb
after 'deploy:restart', 'deploy:restart_resque'
```

#### 有關啟動 worker 的參數

resque 提供了許多參數，讓你可以依照不同的需求執行worker，底下將介紹  

###### 將 worker 執行的結果輸出  

debug 很好用，但個人還是推薦用 rspec 去 debug 吧 XD

```ruby
VVERBOSE=1 QUEUE=* bundle exec rake environment resque:work
```

###### 指定 queue (一條或多條)

多條可以逗號隔開

```ruby
QUEUES=file_serve,warm_cache bundle exec rake environment resque:work
```

\* 則是所有的 queue

```ruby
QUEUES=* bundle exec rake environment resque:work
```

###### polling 的頻率

針對即時性高的需求,以秒計,預設為 5 秒

```
INTERVAL=0.1 QUEUE=foo bundle exec rake environment resque:work
```

###### 多個 worker 同步執行

```
COUNT=5 QUEUE=* bundle exec rake environment resque:workers
```

但官方推薦用 gem "god" 來執行多個 worker  
相關可參考 <a href="https://github.com/defunkt/resque/blob/master/examples/god/resque.god" target="_blank">官方範例</a>  
god 也可以用來解決 worker 掛掉的問題  
有關 god 的整合, 有空再分享 :p  
