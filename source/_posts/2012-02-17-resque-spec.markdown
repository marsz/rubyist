---
layout: post
title: "用 resque 實作背景作業 (測試篇 - Rspec)"
date: 2012-02-17 00:40
comments: true
author: MarsZ
categories: ["Rubygems", "Resque", "Message-queue", "Background-job", "Rspec"]
---
不管用什麼方式實作 background job 後  
令人頭痛的是很難知道 worker 在執行工作時有沒有噴 exception  
因此測試就顯得更為重要, 每次佈署前必須做好完整的測試, 以減少 worker 出錯  
當然也可以搭配 Exceptional 或 Aribrake 之類的雲端服務來進行監控...  
<!-- more -->

resque 的測試有專門的 gem, 用來在測試環境下模擬 message queue 和 worker  

resque_spec : <a href="https://github.com/leshill/resque_spec" target="_blank">https://github.com/leshill/resque_spec</a>  

#### 安裝

```ruby Gemfile
group :test do
  gem 'resque_spec'
end
```

這裡要注意一點, 不要在 development 環境下 bundle resque_spec, 即 Gemfile 中的 resque_spec 只能 group 在 test 下, 否則執行 Resque.enqueue 就不會送到 redis 而是送進 resque_spec 中  

#### 使用

resque_spec 的作法是讓原本丟工作到 mq 上的 Resque.enqueue 改為丟到 resque_spec 模擬的 mq 中  
因此 Gemfile bundle 後, 就可以直接在 rspec 中使用, 以下將介紹 rspec 中常用的語法  

###### 清空 mq 所有的 job

```ruby
describe "#foo" do
  before do
    ResqueSpec.reset!
  end
end
```

###### 檢查某個 worker 的 job 數

```ruby
  FooWorker.should have_queue_size_of(2)
```

###### 檢查某個 worker 的 queue 中應該要有的 job

```ruby
  FooWorker.should have_queued(foo.id, "bar")
```

foo.id, "bar" 都是 enqueue 的參數

```ruby
  # some where enqueue
  Resque.enqueue(FooWorker, foo.id, "bar")
```

###### 立即執行不送 queue

用 "with_resque"

```ruby
it "should works" do
  with_resque do
    @foo.run
  end
end
```

假設 run 的內容是

```ruby
def run
  Resque.enqueue(FooWorker, id) 
end
```

在 with_resque 內, Resque.enqueue(FooWorker, id) 相當於 FooWorker.perform(id)

###### 執行 queue 裡面所有的 job 

```ruby
it "should  works" do
  @foo.run
  ResqueSpec.perform_all(:queue_foo)
end
```

"queue_foo" 是 Worker 中定義的 queue name

#### 整合 ResqueMailer

gem "resque_mailer" 會將 mailer 中的 deliver 行為內容改為 Resque.enqueue  
但是在 test 環境中, resque_mailer 則會還原 deliver 原本的行為  
所以為了 rspec 中也可以測試 mailer 是否 enqueu, 要在 resque_mailer 中的 config 將原本排除掉的 test 環境加入 resque_mailer 的運作中  

```ruby  config/initializers/resque_mailer.rb
Resque::Mailer.excluded_environments = []
```

-------------------------

一般我們都會將複雜或吃效能的工作丟到背景執行  
而複雜的工作又常常需要 debug  
因此平常應該要養成寫 spec 的習慣  
才不會讓自己跟這些複雜的功能過不去 :p

