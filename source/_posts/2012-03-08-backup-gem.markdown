---
layout: post
title: "用 backup gem 做自動化備份"
date: 2012-03-08 11:57
comments: true
categories: ["Rubygems", "Backup"]
---
source: <a href="https://github.com/meskyanichi/backup" target="_blank">https://github.com/meskyanichi/backup</a>  
和 <a href="http://godrb.com/" target="_blank">God</a> 一樣是一個獨立的 gem，因此也可用於其他語言  
<!-- more -->
`backup` 的特色:  

1. 可備份 `Redis`、`MongoDB`、`Mysql` 等 DB
2. 可備份檔案、目錄
3. 備份可儲存於 local disk、`Amazon S3`、`Dropbox` 或透過 `ftp`.`scp` 等協定將檔案上傳

範例檔的備份標的: 

1. `Mysql`
2. 使用者上傳之頭像圖檔儲存於 `Rails` public/uploads
3. 備份檔案上傳至 `Amazon S3`

#### 安裝

`gem install backup`


#### 範例

打開空檔案即可使用

```ruby backup_config.rb
Backup::Model.new(:my_app, 'db data of my app') do
  # mysql 備份
  database MySQL do |db|
    db.name               = "my_app_db"
    db.username           = "root"
    db.password           = "12341234"
    db.host               = "127.0.0.1"
    db.port               = 3306
    db.socket             = "/tmp/mysql.sock"
  end

  # user 頭像檔案  
  archive :uploads do |archive|
    archive.add "/path/to/my_app/public/uploads"
  end  

  # 上傳至 S3  
  store_with S3 do |s3|
    s3.access_key_id      = "12341234"
    s3.secret_access_key  = "!@#$%^&*("
    s3.region             = "us-east-1"
    s3.bucket             = "my_app_backup"
    s3.path               = '/'
    s3.keep               = 50
  end

  # 壓縮打包設定
  compress_with Gzip do |compression|
    compression.best = true
    compression.fast = false
  end
  
end
```

執行 

```ruby
backup perform -t my_app -c backup_config.rb
``` 

就可進行備份  
  
若要做 `Rails` 定期備份, 再將指令整合於 `whenever` 即可  
另外再提供儲存於 local disk 的範例

```ruby backup_config.rb
  store_with Local do |local|
    local.path = "/path/to/back/dir"
    local.keep = 50
  end
```
