---
layout: post
title: "Redis 在 Passenger 重起後出現 'reconnect to Redis after forking' 的錯誤訊息"
date: 2013-04-13 22:02
comments: true
categories: ["redis", "passenger"]
---
自 `redis` 升級，每次透過 `capistrano` 佈署後，連線 server 都會噴出 "Tried to use a connection from a child process without reconnecting. You need to reconnect to Redis after forking." 的錯誤訊息。

<!-- more -->

原因在於 `redis` 的 <a href="https://github.com/redis/redis-rb" target="_blank">client gem</a>。  

因此必須在每次 passenger restart 後，也讓 redis client 進行重啓。可參考以下，必須於 initializer 或 application.rb 中執行。  

```ruby
if defined?(PhusionPassenger)
  PhusionPassenger.on_event(:starting_worker_process) do |forked|
    if forked
      Redis.current = Redis.new(:host => "127.0.0.1", :port => 6379)
    end
  end
end
```

除了 `redis` 之外，其他透過 redis 應用的 gem 也都會有類似的問題，例如 `resque`、`redis-store`、`redis-object`、`sidekiq` 等。  
