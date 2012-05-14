---
layout: post
title: "Rails Spec (4) model validations 的 spec"
date: 2012-02-04 16:45
comments: true
author: MarsZ
categories: ["Rspec", "Rubygems", "Testing", "Model"]
---
本篇將介紹以最原始, 沒有用任何外掛下的 model validation spec 寫法  
若覺得小弟有更好的寫法, 也歡迎不吝指教 :)  
<!-- more -->

範例中所使用的 matchers:

be_valid  
be_true  
be_false  

model檔: User

```ruby
class User < ActiveRecord::Base
  validates_presence_of :name
  validates_length_of :name, :in => 1..20
  validates_uniqueness_of :email
end
```

spec 寫法 (spec/models/user_spec.rb)

```ruby
describe User do
  describe "validations" do
    before do
      @user = User.new :name => "foo", :email => "bar@foo.com"
    end
    it "should be valid" do
      @user.should be_valid
    end
    it "should not validate presence of :name" do
      @user.name = nil
      @user.should_not be_valid
    end
    it "should not validate length of :name" do
      @user.name = "foo" * 20
      @user.should_not be_valid
    end
    it "should not validate uniqueness of :email" do
      @user.save
      User.new(@user.attributes).should_not be_valid
    end
  end
end
```

be_valid 可以讓 @user 先把 save 以前的 validation 都跑過, 並且預期 return true  
當然若要直接 save 以預期結果的話, 可以改寫成 

```ruby
@user.save.should == true
@user.save.should == false
```

或(可讀性較佳的寫法)

```ruby
@user.save.should be_true
@user.save.should be_false
```
  
謎之聲: 每個 validation 都要這樣寫好累 XD  
  
當然上述的範例中除了麻煩之外, 當然 User 有追加了新的 validation 時, before 的 User.new 也要跟著對應, 倘若有很多需要 create user model 的 spec 時, 改起來就會很痛苦  
  
因此上述的兩個 issue, 將會在下兩篇將會介紹透過 factory_girl_rails 和 shoulda-matchers 這兩個 gem 解決
