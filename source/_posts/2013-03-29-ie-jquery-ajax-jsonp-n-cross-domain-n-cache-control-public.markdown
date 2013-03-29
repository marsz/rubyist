---
layout: post
title: "修正 IE 無法透過 cross-domain AJAX 取得 JSON 物件"
date: 2013-03-29 22:52
comments: true
categories: ["API", "IE", "jQuery", "javascript"]
---

controller 中的 `respond_to` 可用來 return 指定的 format 供 JS 取用 API 資料  
但很不幸的 IE 9 以下在透過 jquery 中的 getJSON 無法取得 return 的 json 物件。  

<!-- more -->

server 端的 action

```ruby
def index
  response.headers["Access-Control-Allow-Origin"] = "*"
  expires_in 1.day, :public => true
  @data = SomeModel.all
  respond_to do |f|
    f.json{ render :json => @data }
    f.xml{ render :xml => @data }
  end
end
```

client 端的 javascript

```javascript
$.getJSON("http://apihost.com/products.json","jsonp", function(products){
   console.log(products);
});
```

結果:  IE9 以下不 work，其他瀏覽器沒問題(生氣)  
  
解決此問題的方法必須例用 jquery jsonp callback 的方式達到目的即可  
因此 server 端的 response 的 content-type 必須是 javascript (見 <a href="http://d.pr/i/66d0" target="_blank">此圖</a>)  

server 端寫法  

```ruby
def index
  response.headers["Access-Control-Allow-Origin"] = "*"
  expires_in 1.day, :public => true
  @data = SomeModel.all
  respond_to do |f|
    f.json{ render :json => @data }
    f.xml{ render :xml => @data }
    f.js{ :json => @data, :callback => params[:callback] }
  end
end
```

client 端的 javascript  

```javascript
$.getJSON("http://apihost.com/products.js?callback=?",function(data){
   console.log(data);
});
```

搞定!!!

server 端其他寫法的補充:  

開啟 cross-domain 的 ajax request  

```ruby
response.headers["Access-Control-Allow-Origin"] = "*"
```

開啟 cache-control 為 public:  

```ruby
expires_in 1.day, :public => true
```


