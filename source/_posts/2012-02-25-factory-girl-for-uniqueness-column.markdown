---
layout: post
title: "factory_girl 的 validates_uniqueness_of 欄位問題"
date: 2012-02-25 09:25
comments: true
author: MarsZ
categories: ["Factory-girl", "Rubygems", "Rspec"]
---
`model` 中有 `validates_uniqueness_of` 的欄位, 在 `factory_girl` 中可透過 `sequence` 來避免兩次以上的 factory 因 validation 沒過而無法產生假資料的情況  

<!-- more -->

```ruby spec/factories/users.rb
FactoryGirl.define do
  factory :user do
    sequence(:email) do |n|
      "foo#{n}@bar.com"
    end
    name "barbar"
    password "12341234"
  end
end
```

`n` 為流水號, 因此可用於 `number` 或 `string` 類型的 column
