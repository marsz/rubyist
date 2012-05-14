---
layout: post
title: "Rspec 設定 request 或 controller 的 spec 中 render view"
date: 2012-03-06 11:05
comments: true
categories: ["Rspec", "Rubygems", "Rspec-controllers", "Rspec-requests"]
---
`rspec rails` 中預設是不會 render view 的, 因此 `response.body` 的內容會是空字串  

<!-- more -->

為了讓 response.body 能實際的把 view render 出來, 可以有個別或預設的作法  

個別  

```ruby
describe UsersController do
  render_views
  it "GET #index" do
    get :index
    response.body.should match("foo")
  end
end
```

預設

```ruby spec/spec_helper.rb
RSpec.configure do |config|
  ....
  config.render_views
  ....
end
```

小弟個人較偏好每個 request / controller 的 spec 都要 render_view  
一方面可以檢驗 view 的正確性  
若 view 中有使用 helper method, 也可以順便檢驗  
