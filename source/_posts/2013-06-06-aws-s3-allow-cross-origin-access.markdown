---
layout: post
title: "讓 S3 允許 cross-domain 存取"
date: 2013-06-06 01:48
comments: true
categories: [AWS]
---

檔案放上 `S3` 後，可能會需要透過 `cross-domain` 存取 (例如 web font)  
這時可以透過 `S3` bucket 的 `CORS` 設定達成  

<!-- more -->

![S3 bucket CORS](http://i.imgur.com/7vjeF3g.png)  

開啟後如同以下內容設定即可開放 `GET` 的跨網域存取  

```xml
<?xml version="1.0" encoding="UTF-8"?>
<CORSConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <CORSRule>
        <AllowedOrigin>*</AllowedOrigin>
        <AllowedMethod>GET</AllowedMethod>
        <AllowedHeader>*</AllowedHeader>
    </CORSRule>
</CORSConfiguration>
```

如果要開放其他 HTTP method...

```xml
<?xml version="1.0" encoding="UTF-8"?>
<CORSConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <CORSRule>
        <AllowedOrigin>*</AllowedOrigin>
        <AllowedMethod>GET</AllowedMethod>
        <AllowedMethod>POST</AllowedMethod>
        <AllowedMethod>PUT</AllowedMethod>
        <AllowedHeader>*</AllowedHeader>
    </CORSRule>
</CORSConfiguration>
```

### 參考資料

<a href="http://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html" target="_blank">http://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html</a>