---
layout: post
title: "CI server - Bigtuna (進階設定篇)"
date: 2012-01-29 12:50
comments: true
categories: ["Continuous Integration", "Bigtuna"]
---
上次提到專案在 bigtuna 上的設定  
這次要示範:  

-   和 github 整合
-   將 build 結果寄送 email  
-   安全性設定  

<!-- more -->

#### 一般的 github hook

專案的編輯頁面中, 有一個欄位叫 "Hook Name"  
用於做 http post request trigger (其實後來發現get也可以)  

![](https://img.skitch.com/20120204-gtdidekib9pitb4yjmyc8rwhjb.png)

舉例來說, bigtuna server 的 domain 假設是 bigtina.marsz.tw 的話
若專案的 hookname 設定為 "myapp"  
則 trigger project build 的 url 則是

```
http://bigtuna.marsz.tw/hooks/build/myapp
```

因此只要到 github.com 的專案頁面裡 "Admin" -> "Service Hooks" -> "Post-Receive URLs" 填入該網址即可

![](https://img.skitch.com/20120204-tc8jni267711r4yi1d7qs3frfw.png)

github 會在每次該專案有 push code 上來時, 送 post 到該 url  

但常見的情況是, 在 github 上的專案裡有多條分支, 而我們只想針對某條分支有 push 時才進行 trigger  

#### 針對不同分支的 github hook

參考: <a href="http://bigtuna.appelier.com/configuration.html" target="_blank">http://bigtuna.appelier.com/configuration.html</a> 最下方區塊  

到 bigtuna 的專案目錄下, 打開 config/bugtuna.yml 加入  

```yml config/bugtuna.yml
production:
  :github_secure: "abcabc"
```

重起 bigtuna 後, hook 在 github 上的 url 則改為  

```
http://bigtuna.marsz.tw/hooks/build/github/abcabc
```

"abcabc" 是你在 github_secure 填的值  
因為 github 在送 post 到 url 時, 會帶上 push 的專案以及分支名稱  
因此 bigtuna 會依據傳送過來的資訊找到對應的 github uri 和 branch 而啟動 build  
而原先設定的 hook name 在這裡就沒有用到了  
github_secure 的設定則是多一層簡單的安全性措施  

#### email config

首先到 bigtuna 專案中設定 email smtp (以下為 gmail smtp 範例)

```yml config/email.yml
production:
  :address: "smtp.gmail.com" 
  :port: 587
  :domain: 'your domain'
  :user_name: 'your email'
  :password: 'ur password'
  :authentication: 'plain'
  :enable_starttls_auto: true
```

設定 email 內容中的 url host  

```yml config/bigtuna.yml
production:
  :url_host: "bigtuna.marsz.tw"
```

重起 delayed job

```ruby
RAILS_ENV=production ./script/delayed_job stop
RAILS_ENV=production ./script/delayed_job start
```

在 project 中的 hook => mailer 打勾, 然後儲存  

![](https://img.skitch.com/20120204-7tb6yfh51qek718t7ewk4uxkh.png)  

儲存後會發現原先打勾的地方多了 Configure  

![](https://img.skitch.com/20120204-qjtnx8xgh66rmd95u87yfcnjqt.png)  

設定 email 時機與對象  

![](https://img.skitch.com/20120204-mcnpxca3ad5r18kb6799m711fk.png)  

注意: 若要寄給多人, 必須以半型逗號隔開, 不能用換行

```ruby
foo@marsz.tw,bar@marsz.tw
```

#### 其他安全性設定

最簡單的方式可以利用 htpasswd  
相關設定請參考 <a href="http://doc.norang.ca/apache-basic-auth.html" target="_blank">htpasswd</a>  
設定完成後, github 的 hook url 也必須一同加上帳號密碼

```ruby
http://user:pssword@bigtuna.marsz.tw/hooks/build/github/abcabc
```

