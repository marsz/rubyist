---
layout: post
title: "Rails Rspec (2) 基本語法與執行"
date: 2012-02-02 14:35
comments: true
categories: [rspec, rubygems, testing]
---
建議在開始本系列文以前先讀過, 以下參考文件與資源  
<!-- more -->

<a href="http://www.slideshare.net/ihower/rspec-7394497" target="_blank">ihower 投影片</a> - 必看!!!!!  
<a href="http://rubydoc.info/gems/rspec-core/2.8.0/file/README.md#" target="_blank">Rspec-core Rdoc</a> - 架構必看!!!  
<a href="https://github.com/rspec/rspec-rails" target="_blank">Rspec Rails</a> - 官方提供的簡單範例 for models, controllers, routes, helpers 等


#### describe 與 it + should

最簡單的 rspec 可由 describe 和 it 組成  
it 裡面必須有 should (或其他 match 語法) 才會構成測試  

小弟在初學 rspec 的時候, 大多透過 should == 和 should_not == 就吃遍天下了 :p  
一般在初學 rspec 時, 需要多注意 describe 和 it 的階層關係, 才會讓你的 spec 可讀性較高

有規劃的 spec

```ruby
describe Foo do
  describe "#get_bar" do
    it "should be nil" do
      Foo.new.get_bar.should == nil
    end
    it "should be Bar" do
      Foo.first.get_bar.class.should == Bar
    end
  end
```

沒有規劃的 spec

```ruby
describe Foo do
  it "#get_bar" do
    Foo.new.get_bar.should == nil # 當這行的測試沒過時, 下面的測試便不會再執行
    Foo.first.get_bar.class.should == Bar
  end
end
```

盡可能讓每個 it 都是針對一種結果進行測試  
描述中用 # 開頭的就表示是 method name  

當漸漸學會其他的 match 語法時, 可讀性就去更高了
例如 <a href="http://apidock.com/rspec/Spec/Matchers/be_a_kind_of" target="_blank">be_a_kind_of</a> 可用於做 is_a_kind_of 的 match  

```ruby
Foo.first.get_bar.should be_a_kind_of(Bar)
```

相當於  

```ruby
Foo.first.get_bar.class.should == Bar
```

前者的可讀性是比較高的  

#### before 與 it

before 必定是在某個 describe 下, 而 before 內的行為會在每個 it 以前執行  
而善用 instance 變數, 可以將 before 產生的變數傳遞給底下每個 it 用

```ruby
describe Foo do
  before do
    @foo = Foo.create!
  end
  describe "#bar" do
    before do
      @bar = Bar.create!
      @foo.bar = @bar
      @foo.save
    end
    it "should be Bar" do
       @foo.bar.should be_a_kind_of(Bar) # 可以拿到 @foo
    end
    it "should be equeal to @bar" do
       @foo.bar.id.should == @bar.id # 也可以拿到 @bar
    end
  end
  it "be a kind of Foo" do
    @foo.should be_a_kind_of(Foo) # 只能拿到 @foo
  end
end
```

此外每個 it 結束後, 都會將此 it (含 before) 內對 test db 的修改通通 rollback, 因此我們可視為每個 it 執行前 (before 前), test db 都會是空的

#### 執行測試

必須注意:  

  1. config/database.yml 必須要有 test env 的設定, 且 database 不能和 migrate 一樣
  2. 執行前若 development 有 migrations 沒跑的話, 必須先跑完

跑 spec/ 下的所有測試  

```
bundle exec rake spec
```

跑單一檔案的 spec

```
bundle exec rspec spec/models/foo_spec.rb
```

跑單一檔案時, 不會先重新 load db schema, 因此若有新的 migrations 在 development 跑過後, 必須先 rake spec 後才能跑單一檔案的 rspec

更多 rspec 指令的應用

```
bundle exec rspec -h
```
