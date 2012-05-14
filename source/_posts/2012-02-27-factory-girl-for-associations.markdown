---
layout: post
title: "factory_girl 中的 model 關連 (association)"
date: 2012-02-27 09:42
comments: true
author: MarsZ
categories: ["Factory-girl", "Rubygems", "Rspec"]
---
不論是 `one-to-one` `one-to-many` 或 `many-to-many`, 我們都希望 model 中的關連也能反應到 `factory_girl` 的定義中  

<!-- more -->

方法如下:  (brand has many products)

```ruby spec/factories/brands.rb
FactoryGirl.define do
  factory :brand do
    sequence(:name) { |n| "brand name #{n}" }
  end
end
```

```ruby spec/factories/products.rb
FactoryGirl.define do
  factory :products do
    name "product name"
    brand do
      Factory :brand
    end
  end
end
```

```ruby app/models/product.rb
class Product < ActiveRecord::Base
  belongs_to :brand  # brand_id
end
```

如此只要每次 `Factory(:product)` , 該 product 的 brancd 也會透過 brancd 產生, 而不需要再每次的 factory 中去進行關連了 `Factory(:product, :brand => Factory(:brand))`
