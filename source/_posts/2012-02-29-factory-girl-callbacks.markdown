---
layout: post
title: "Factory Girl 的回呼 (callbacks)"
date: 2012-02-29 10:18
comments: true
author: MarsZ
categories: ["Factory-girl", "Rubygems", "Rspec"]
---
在寫測試中, 有許多的情境, 可能會需要在產生測試用資料後做許多處理, 因此 `Factory Girl` 本身也提供了 `callback` 機制...

<!-- more -->

```ruby
FactoryGirl.define do
  factory :user do
    sequence(:email) { |n| "foo#{n}@bar.com" }
    name "barbar"
    password "12341234"
    after_create do |user|
      # callback code block here
    end
  end
end
```

詳細使用方式可見 <a href="https://github.com/thoughtbot/factory_girl/blob/master/GETTING_STARTED.md" target="_blank">官方說明文件</a>
