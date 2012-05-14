---
layout: post
title: "Bigtuna 保持 delayed_job 持續執行"
date: 2012-02-23 08:59
comments: true
author: MarsZ
categories: ["Continuous Integration", "Bigtuna", "Delayed Job", "God"]
---
`BigTuna` 是透過 <a href="https://github.com/tobi/delayed_job" target="_blank">delayed_job</a> 讓所有 build 中的 process 的能夠正常運作  
當 `delayed_job` 因為某種不明因素終止時, 就得再 ssh 到 server 重啟 `delayed_job`...  

<!-- more -->

<a href="http://godrb.com/" target="_blank">God</a> 是一套獨立的 `rubygems`   
可以讓你在 `linux` 環境下監控某 process, 並且保持其持續且正常運作  
因此我們可透過 `God` 來使 `delayed_job` 不被意外終止  

#### 安裝

```
sudo su -
gem install god
```

#### BigTuna 的 god-ruby 範例檔

```ruby
God.watch do |w|
  w.uid = "passenger"  # bigtuna 在 linux 下的 user id
  w.gid = "passenger"  # bigtuna 在 linux 下的 group id
  w.name = "bigtuna-delayed_job"  # 在 god 中的名稱
  # 執行 delayed_job 的指令
  w.start = "cd /path/to/bigtuna && RAILS_ENV=production ./script/delayed_job start"
  w.keepalive 
end
```

#### 以 root 身分執行 god

```
god -c delayed_job.rb
```

執行後可透過 `ps axu|grep "god"` 或 `ps axu|grep "delayed_job"` 看到 `god` 啟動 `delayed_job` 的相關 process

 
