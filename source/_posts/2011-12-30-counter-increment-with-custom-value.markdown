---
layout: post
title: "實作 sql 中的數值欄位的遞增(counter increment)"
date: 2011-12-30 17:07
comments: true
author: MarsZ
categories: ["ActiveRecord", "Counter", "SQL"] 
---
針對 db 的數值欄位做直接加減以避免 inconsistent update 問題
即想在 sql 中實作出
```sql
UPDATE users SET score = score + 1 WHERE id = 1
```
<!-- more -->

rails 在實作 counter cache 中有 api 可用
```ruby
User.increment_counter "score", user.id
```

對應 sql:
```sql
UPDATE users SET score = score + 1 WHERE id = 1
```

###### 參考
<a href="http://apidock.com/rails/ActiveRecord/CounterCache/increment_counter" target="_blank">http://apidock.com/rails/ActiveRecord/CounterCache/increment_counter</a>

當然, 遞減的 method 相對是 decrement_counter

若增減值大於 1 的話, 則可用 update_counters 來實作
```ruby
User.update_counters user.id, :score => 3
```

輸出 sql
```sql
UPDATE users SET score = score + 3 WHERE id = 1
```

update_counters 可以一次對多個欄位做增減
```ruby
User.update_counters user.id, :score => 3, :karma => 6
```

###### 參考
<a href="http://apidock.com/rails/ActiveRecord/CounterCache/update_counters" target="_blank">http://apidock.com/rails/ActiveRecord/CounterCache/update_counters</a>
