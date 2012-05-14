---
layout: post
title: "databse 資料以 yaml 格式匯入/匯出"
date: 2011-12-28 14:07
comments: true
categories: ["Rubygems","YAML","Database","Backup"]
---

在備份資料的過程中常用到的一個 gem
可透過 rake 指令將 db 資料以 yaml 格式進行匯出與匯入

## yaml_db

github: <a href="https://github.com/ludicast/yaml_db" target="_blank">https://github.com/ludicast/yaml_db</a>

<!-- more -->

## 安裝 (rails 3)

```ruby
gem 'yaml_db'
```

```
bundle install
```

## 匯出

把資料匯出到 db/data.yml

```
bundle exec rake db:data:dump 
```

匯出不同環境的 database (設定在 config/database.yml, 預設是 development)
```
bundle exec rake db:data:dump RAILS_ENV=production
```

若要依照一個 table 一個檔案匯出..
(2011/12/28 notice: 目前此方法在 1.9.2 + 3.1.0 的環境下測試會有一些問題)
```
cd path/to/dump_dir  # cd 到欲匯出的 dir 下, 理論上依然是在專案目錄內
db:data:dump_dir
```

## 匯入

把資料從 db/data.yml 匯入
```
bundle exec rake db:data:load
```

匯入不同環境的 database
```
bundle exec rake db:data:load RAILS_ENV=production
```

匯入某個目錄下的所有的 yaml (原理同 rake db:data:dump_dir)
(2011/12/28 notice: 此方法在 1.9.2 + 3.1.0 下仍有問題)
```
cd path/to/yamls_dir # cd 到 yaml 檔存放的 dir
bundle exec rake db:data:load_dir
```

## 其他

* 配合 whenever 可做定期備份, 並且將 yml 檔上傳至其他處存放
