---
layout: post
title: "CI server - Bigtuna (專案設定篇)"
date: 2012-01-28 12:06
comments: true
author: MarsZ
categories: ["Continuous Integration", "Bigtuna"]
---
上回提到 Bigtuna 的安裝  
這次示範如何針對一個專案的自動化測試進行設定  
<!-- more -->
首先點選右上角的 "new project"  

![](https://img.skitch.com/20120204-gxm36jskatwejffbr9emf3h13.png)  

填好重點資訊, 儲存  

![](https://img.skitch.com/20120204-nkft6nwwm4323bd4grrix5xxdr.png)

儲存後仍會停留在同一頁, 拉到下面會看到  

![](https://img.skitch.com/20120204-bh4iib8dj31ac9wsymi4nc44m7.png)

到目前為止,bigtuna可自動將專案從 github clone 下來(並且針對設定的分支)  
因此這裡的 configuration 則是在 clone 下來後要做的動作(一般就是跑測試啦)  

以下提供小弟的 steps 範例  

```
# 用 rvm 切換到專案所使用的 ruby 版本
rvm 1.9.2-p290
# 將專案的設定檔 copy 到 clone 下來的專案目錄中
cp /home/marsz/projects/myapp/config/database.yml %build_dir%/config/database.yml
# bundle install for test 環境
bundle install RAILS_ENV=test
# 將資料庫schema load 到 mysql 中
bundle exec rake db:setup RAILS_ENV=test
# 進行 rspec 測試
bundle exec rake spec RAILS_ENV=test
```

設定檔的部份, 因為我們不會將 db user/password 和 email smtp config 等設定 commit 到版本管理中, 因此每個專案在 bigtuna server 上的設定檔都要先手動建立, 以上述範例, 我將 myapp 這個專案的設定檔固定放在 /home/marsz/projects/myspp/config 下  

而 %build_dir% 則是 bigtuna 提供的變數, 指向到每次 clone myapp 下來的臨時目錄  

此外, 所有指令的環境都是在 test 下, 因此 config 設定也都必須針對 test  

"rake spec" 設針對 gem Rspec 所跑的自動化測試, 也是測試的重點所在, 前面的步驟都是為了讓 Rspec 能順利跑起來  

儲存後回到 "Home" 會看到專案列表, 在 bigtuna 中, 顏色的含意如下:  
藍色 - 尚未執行  
黃色 - 執行中
綠色 - 執行完畢 (沒error)  
紅色 - 執行完畢 (有error)  

![](https://img.skitch.com/20120204-rxha851d97n1bi7kjjjhtt5hef.png)  

點進去 project 後, 右上角有一個 "BUILD NOW", 可進行手動 build  

![](https://img.skitch.com/20120204-mi4mgi3fwswdwg5xchaa3gss9c.png)  

點了 "BUILD NOW" 我們就會看到專案開始進行 build  

![](https://img.skitch.com/20120204-pkhykjqc63thr3rkduqnymbus.png)  

若 build 結果有問題, 會看到"紅燈", 點進去可以看到該 build 出現的錯誤訊息  

![](https://img.skitch.com/20120204-fi6b2tte3qs7x19fgjyu8h9wpa.png)  
![](https://img.skitch.com/20120204-ks36ja1g5i4wydaiq8qxjdrke4.png)  

如此一個專案的 CI 設定算是完成了  
下一篇將介紹如何自動化進行 build, 整合 github 與 email 通知

##### 補充說明  

若你在 github 上的專案是 private 的話  
必須讓 ci server 能夠 ssh github  
相關設定可參考<a href="http://help.github.com/mac-set-up-git/" target="_blank">官網說明</a>
