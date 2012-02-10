---
layout: post
title: "Rails Rspec (3) pending 的使用"
date: 2012-02-03 16:06
comments: true
categories: [rspec, rubygems, testing]
---
pending 的使用可先參考 <a href="https://github.com/dchelimsky/rspec/wiki/Pending-Examples" target="_blank">官方文件</a>  
一般 generator 產生的 spec 檔都會先幫你把 pending 加好  
<!-- more -->

```ruby
describe Foo do
  pending "add some examples to (or delete) #{__FILE__}"
end
```

如此便可在你跑 spec 後, 顯示有幾個 pending 尚未實作  

```
1 example, 0 failures, 1 pending
```

因為不是每個專案都會讓你有時間寫 spec  
等到有時間寫的時候, 可能會因為累積了太多而不知該從何寫起  
所以 pending 的功用可讓你在未來能透過 pending 的麵包屑回頭逐一把 spec 補上  
  
因此無論再怎麼趕怎麼忙, 也必須先將 pending 寫上  
  
pending 可分幾個層次如下...  

#### 整個 class 的 pending

用於 lib class 較多, 當然遇到超趕的時候 controller, model 也都會整個 pending ~ XD  

```ruby
describe FoosController do
  pending
end
```

#### 某個 method 的 pending

新加的 method, 或 method 改寫了, 原先的 spec 不敷使用  

```ruby
describe Foo do
  describe "#bar" do 
    pending
  end
end
```

或

```ruby
describe Foo do
  pending "#bar"
end
```

一般都是後者的寫法

#### method/action 內的某個 case

針對單一 method/action 寫測試, test case 數通常取決於有多少 if else 在裡頭, 當然遇到比較特殊的 method/action 時, 可能也會有幾個 test case 是為預期錯誤而做的  

```ruby
describe Foo do
  describe "#bar" do
    it "should be a kind of Bar" do
      ....
    end
    pending "should raise error if bar_id is nil"
  end
end
```

另外, method 只針對 public methods 寫, 一般 protected 和 private method 都不寫 spec  
嚴謹的專案, 對於 model 的 association, validation, callbacks, scope 也都會寫對應的 spec  
我的習慣是, 在每次 commit 前檢查本此修改是否需要補 spec 或 pending, 補完後才一起 commit
