---
layout: post
title: "Rspec 檔案上傳的測試"
date: 2012-03-04 10:45
comments: true
categories: ["Rspec", "File-upload", "Carrierwave", "Controller"]
---
`controller` 的測試中, 總是免不了要處理檔案上傳, rails 中的測試環境當然也提供了這樣的機制

<!-- more -->

#### 範例

假設要模擬 view 中 `<input type="file" name="user[avatar]" />` 的檔案上傳

```ruby spec/requests/users_controller_spec.rb
querys = { :user => { :avatar => fixture_file_upload("/example.jpg","image/jpeg") } }
post :create, querys
response.should be_success
```

`/example.jpg` 會抓 `spec/fixtures/example.jpg` 做為上傳用的範例圖檔  
`image/jpeg` 則是 `MIME type`  

亦可寫在 `factory` 中

```ruby
Factory :video_file do
  file { fixture_file_upload '/test.png', 'image/png' }

  after_create do |video, proxy|
    proxy.file.close
  end
end
```

相關可參考<a href="http://apidock.com/rails/ActionDispatch/TestProcess/fixture_file_upload" target="_blank">Api Dock</a>


