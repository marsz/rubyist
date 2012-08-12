---
layout: post
title: "devise 的 user 登入免驗證密碼"
date: 2012-08-09 17:15
comments: true
categories: [Rubygems, Devise, Omniauth]
---
`Devise` 加上 `Omniauth` (或其他 Open ID 登入機制) 時，資料庫的密碼欄位可能會需要為空值，因此可在 user 的 model 中覆寫 `password_required?` (為 public method) 即可。

<!-- more -->

```ruby app/models/user.rb
  def password_required?
    return (self.facebook_id ? false : true)
  end
```

該範例之邏輯為，有 facebook id 時密碼欄位可為空值。
