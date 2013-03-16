---
layout: post
title: "我寫的 gem - venus 簡介"
date: 2013-03-13 13:12
comments: true
categories: [Rubygems]
---

因受到了<a href="http://railsapps.github.com/rails-composer/" target="_blank"> rails composor </a> 的影響，決定也來寫一個客製化的 rails generator。  
但後來又覺得只能在開新專案用的話，使用頻率也不太高...  

<!-- more -->

鑿於在許多專案中，總是為了要安裝某些 gem 而必須再回去翻寫過專案中的 code 或上官網找範例 code 做 copy/paste + 修改，於是我決定寫一個 generator 來設定這些常用的 gem，僅需要如同 rails composor 一樣回答一些問題就可以把所有的設定弄到好。    

因此 venus 因應而生  

安裝: <a href="https://rubygems.org/gems/venus" target="_blank">https://rubygems.org/gems/venus</a>    
源碼: <a href="https://github.com/marsz/venus" target="_blank">https://github.com/marsz/venus</a>    

以下逐一介紹各個指令   

`rails generate venus:init`  

專案初始化設定，例如移除 public/index.html，安裝必要的 gem 例如分頁用的 `kminari`，剛 new 好的 rails 專案適合執行此指令。  

`rails generate venus:mysql`  

設定 mysql，包含從 `Gemfile` 安裝 mysql2，設定 `database.yml` 以及 `database.yml.example`，最後再將 `config/database.yml` 加到 .gitignore 中。  

`rails generate venus:paginate`  

單獨安裝分頁用的 gem - `kminari`，也會問你要不要產生客製化的 view 出來。  

`rails generate venus:simple_form`   

view 使用 form 必備的 <a href="https://github.com/plataformatec/simple_form" target="_blank">simple_form</a>，預設會產生 bootstrap wrapper 的設定檔。也會詢問是否要順便安裝 <a href="https://github.com/ryanb/nested_form" target="_blank">nested_form</a>，安裝 nested_form 需要在 js 中 require gem 裡面包好的 JS，所以也會詢問 require code 和 example code 要放的檔案路徑。  

`rails generate venus:settingslogic`  

gem - <a href="https://github.com/binarylogic/settingslogic" target="_blank">`settingslogic`</a>。可以將多個 yml 的設定檔放在此 gem 所讀取的 yaml 下，使其能夠容易管理。此 generator 同時也會產生 .example 檔以及加入 .yml 到 .gitignore 下。

`rails generate venus:rspec`  

安裝並且設定 `rspec`，並且移除 test 目錄，在 `config/application.rb` 中設定 generate controller 時，不自動產生 assets files 和 helper。  

`rails generate venus:devise`  

安裝並且設定 scope 為 user 的 devise gem。  

`rails generate venus:omniauth`  

會先檢查是否有安裝 settingslogic 以及 devise，然後再安裝 omniauth，過程中會詢問 facebook / twitter / github 登入的安裝與否。  

`rails generate venus:redis`  

安裝並且設定 redis client gem <a href="https://github.com/redis/redis-rb" target="_blank">redis-rb</a> 。  

`rails generate venus:aws`  

安裝並且設定 Amazon <a href="https://github.com/aws/aws-sdk-ruby" target="_blank">官方的 ruby client </a>。過程中會先檢查 settingslogic，並且將 api key 和 secret 存到其中。最後也會詢問否需要順便安裝 Amazon Simple Email Service (SES)，用來發 email。  

`rails generate venus:carrierwave`  

安裝並且設定 carrierwave，其中包含 rmagick 進行縮圖，以及 fog 將檔案上傳至S3。過程中會安裝 aws-sdk，所以也會順便詢問 SES 的設定 :p。最後會產生一個 `venus_uploader.rb` 做為範例。  

`rails generate venus:jqueryui`  

安裝 jquery ui 的 gem。其中會詢問是否要安裝 datepicker，包含產生 trigger 用的 js 範例 code。gem 的 參考連結 => <a href="https://github.com/joliss/jquery-ui-rails" target="_blank">https://github.com/joliss/jquery-ui-rails</a>。 (若有要安裝 datepicker 的話，也會詢問語系)  

`rails generate venus:chosen`  

安裝 <a href="https://github.com/tsechingho/chosen-rails" target="_blank">chosen-rails</a> 並且提供 trigger chosen 的範例 js code。  

`rails generate venus:bootstrap`  

安裝 `twitter bootstrap` ，包含 gem 設定、 assets require，最後產生範例 layout 檔於 `app/views/layouts/` 下。內建提供 <a href="http://wrapbootstrap.com/preview/WB0F35928" target="_blank">unicorn admin</a> 的版型套用，蓋後台超爽 XD  

TODOs
=====

對於此 gem 未來將會逐漸加入我自己寫的 best practice :p 也望各位大大能不吝指教或回報 bug :D  

* sidekiq : 強大的 background job gem  
* unicorn : 配合 capistrano 讓佈署時可以 zero down time
* <a href="https://github.com/airblade/paper_trail" target="_blank">paper_trail</a> : 對 model 進行版本控制的 gem


