---
layout: post
title: "利用 Swagger 產生互動式 API 文件"
date: 2013-05-04 20:48
comments: true
categories: ["API"]
---

<a href="https://developers.helloreverb.com/swagger/" target="_blank">Swagger</a> 是一套由 HTML + Javascript 撰寫的 REST API 文件的框架。 (core 是以 scala 寫的)

<!-- more -->

從 <a href="http://petstore.swagger.wordnik.com/" target="_blank">Demo 頁</a> 可了解到 Swagger 不僅僅提供 API 的規格資訊，也提供互動介面讓開發者可以直接輸入參數進行測試。  
<img src="http://i.imgur.com/a5vAGKz.png" />  

亦支援 GET & POST 以外的 http method (PUT、DELETE)。  

<img src="http://i.imgur.com/L3c5TTt.png" />  

Demo 頁面的 source code 在<a href="https://github.com/wordnik/swagger-ui" target="_blank"> Github </a>上。也可以參考 wordnik 的<a href="http://developer.wordnik.com/docs.html" target="_blank"> 官方正式 API 文件 </a>。  

API 文件內容完全是透過 ajax 取得 JSON data 產生的，因此必須提供 server 吐出符合 Swagger 文件中所定義的 JSON 格式，讓前端網頁能夠生成 API 文件的介面。  

<img src="http://i.imgur.com/iC3LFIL.png" border="1" style="border-color:#000000" />  

在 Swagger 文件中所定義的物件有:

1. `doc`: 代表整份 API 文件 (<a href="https://github.com/wordnik/swagger-core/wiki/Resource-Listing" target="_blank">規格文件</a>)  
2. `resource`: API 文件下的分類目錄 (<a href="https://github.com/wordnik/swagger-core/wiki/API-Declaration" target="_blank">規格文件</a>)  
3. `api`: 每一條 REST Request (<a href="https://github.com/wordnik/swagger-core/wiki/API-Declaration" target="_blank">規格文件</a>)  
4. `parameter`: `api` 下的每個參數 (<a href="https://github.com/wordnik/swagger-core/wiki/Parameters" target="_blank">規格文件</a>)
5. `model`: 自訂的 return data type (<a href="https://github.com/wordnik/swagger-core/wiki/Datatypes" target="_blank">規格文件</a>)  

鑿於定義相當繁多且複雜，Swagger 本身有提供<a href="https://github.com/wordnik/swagger-codegen/tree/master/samples/server-generator" target="_blank">部分語言的 API 文件生成後台</a> (Ruby 開發者可選擇 sinatra)，讓開發者能夠花最少的時間開始使用 Swagger。  

但筆者仍覺得 Swagger 使開發者進入狀況的門檻仍高，因此花了點時間以 rails app 開發 Swagger json data 的管理後台生成 swagger API 文件，提供後台的 web 操作介面來產生 Swagger 的 API 文件。  

Github: <a href="https://github.com/marsz/swagger-rails" target="_blank">https://github.com/marsz/swagger-rails</a>  
Demo: <a href="http://swagger.5fpro.com/" target="_blank">http://swagger.5fpro.com/</a>  

功能特色:  

1. FB 登入/註冊，單一帳號下可建立多筆 `doc`  
2. 自訂 API 實際測試的目標網域  
3. 可設定獨立網域 (FQDN)，若沒有設定系統也會自動產生一組給你用  
4. `resource` 可排序  

讓我們 step by step 來建立 API 文件吧。  
範例是小弟在早期自己寫的 API，用來提供台灣縣市鄉鎮的資料庫。 

1. 建立 `doc`，內容如下:  
  <img src="http://i.imgur.com/dM1Ori8.png" />  
2. 建立 `doc` 後，系統會提供你 API 實測時的網址格式  
  <img src="http://i.imgur.com/RNsHmMx.png" />  
3. 建立 `resource` (圖略)， `resource` 可以用來當做是管理許多 API 的目錄  
4. 建立 `api`: 選擇 http method 和 path  
  <img src="http://i.imgur.com/x9wfS0U.png" />  
  若 API 參數在 path 中，可用大括號包起來，必且給予參數名稱  
5. 填寫 return data type，若為某個 model 的 array 可以以圖內的格式表示  
  <img src="http://i.imgur.com/5TyRs6L.png" />  
6. api path 內有參數的話，底下的 parameters 也必須增加 `parameter type` 為 `path` 的參數 (如圖)，名稱和大括號內對應  
  <img src="http://i.imgur.com/98IsmNe.png" />  
7. 有關 `model` 的定義，可在 doc 頁找到，這裡的定義與否不會影響文件的正常顯示，有定義的話文件也會顯示出來，內容也就更完整囉  
  <img src="http://i.imgur.com/F1vZR2E.png" />  
8. 點擊 doc information 內的連結即可看到結果(<a href="http://gaia-doc.5fpro.tw/#!/3/api_6_get_2" target="_blank">http://gaia-doc.5fpro.tw/#!/3/api_6_get_2</a>)  
  <img src="http://i.imgur.com/8x0rLQX.png" />  
9. 當然，API 實測要有資料，必須實作 <a href="http://gaia.5fpro.tw/cities/1.json" target="_blank">http://gaia.5fpro.tw/cities/1.json</a> 回傳的 JSON data 才行。

目前的 swagger-rails 仍有許多待增加的功能:  

1. 多人協作
2. 修改記錄  
3. 自訂前端 CSS
4. 後台介面優化
5. 100% 實作 Swagger 所有支援的格式

最後附上筆者目前正式對外開放的 API 文件給各位參考:  

1. <a href="http://api-doc.thewall.tw/" target="_blank">THEWALL 這牆音樂</a>  
2. <a href="http://gaia-doc.5fpro.tw/" target="_blank">台灣縣市鄉鎮資料庫</a>, 含郵遞區號  
3. <a href="http://medusa-doc.5fpro.com/" target="_blank">爬蟲系統</a> (暫不開放申請)  

筆者目前透過自己蓋的 `swagger-rails` 用於內部溝通居多，不論在 mobile app 開發或跨語言的資料傳遞，透過 Swagger 介面的 API 實測功能，使用者很快就可以了解每條 API 的取用方式與資料格式，大大減少了溝通的成本。  

Swagger 參考資料:

1. <a href="https://github.com/wordnik/swagger-core/wiki" target="_blank">Swagger Specification</a>  
2. <a href="https://github.com/wordnik/swagger-core/wiki/Downloads" target="_blank">Swagger Downloads</a>  
3. <a href="https://developers.helloreverb.com/swagger/" target="_blank">Official Site</a>  