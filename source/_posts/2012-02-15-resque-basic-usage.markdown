---
layout: post
title: "用 resque 實作背景作業 (基本使用篇)"
date: 2012-02-15 15:36
comments: true
author: MarsZ
categories: ["Rubygems", "Resque", "Message-queue", "Background-job"]
---
上篇提到如何進行 redis, resque, worker 的設定安裝 
本篇將介紹基本使用方式  

<!-- more -->

#### 參考連結

RailsCast - 影片: <a href="http://railscasts.com/episodes/271-resque" target="_blank">Resque</a>
RailsCast - 文字: <a href="http://railscasts.com/episodes/271-resque?view=asciicast" target="_blank">
Resque</a>
官方文件: <a href="https://github.com/defunkt/resque" target="_blank">Github</a>

#### 基本使用

情境: 文章內容更新後, 要重新計算其價值(p幣~XD)

先寫好 worker 要做的事情, worker 可以是任何 class

```ruby app/workers/post_evaluate.rb
class PostEvaluate

  @queue = :post_evaluate

  def self.perform(post_id, total_time_seconds)
    evaluate Post.find(post_id), total_time_seconds
  end

end
```

worker 必備兩個部分:
1. @queue 指定 queue 的名稱
2. class method: perform 執行 job 內容, 參數可自定

接下來是如何把 job 丟上 queue

```ruby app/models/post.rb
after_save :evaluate

def evaluate
  Resque.enqueue PostEvaluate, self.id, total_secs
end
```

Resque.enqueue [worker class], [argument 1], [argument 2], .....

相當於 worker 執行了 PostEvaluate.perform(self.id, total_secs)

#### 注意事項

enqueue 的參數類型儘可能簡單: Fixnum, String, Array, Hash  
不要將整個 model 或 class instance 丟到 mq 上  
redis 無法儲存像 model 這樣複雜的變數類型  

下一篇將介紹如何寫測試(Rspec)
