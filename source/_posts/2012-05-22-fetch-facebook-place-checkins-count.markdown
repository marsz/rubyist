---
layout: post
title: "取得 Facebook 地點的打卡數"
date: 2012-05-22 15:14
comments: true
categories: ["Facebook"]
---
參考 <a href="http://developers.facebook.com/docs/reference/api/" target="_blank">Facebook Graph Api 文件</a>  
<!-- more -->

#### 透過關鍵字搜尋某個地點

```
https://graph.facebook.com/search?q=[關鍵字]&type=place&access_token=[ur access token]
```

#### 透過經緯度搜尋某個點範圍內的地點

```
https://graph.facebook.com/search?q=[關鍵字]&type=place&center=37.76,-122.427&distance=1000&access_token=[ur access token]
```

#### 搜尋結果的 JSON 範例

```
{
   "data": [
      {
         "name": "Q Square \u4eac\u7ad9\u6642\u5c1a\u5ee3\u5834",
         "category": "Local business",
         "location": {
            "street": "\u53f0\u5317\u5e02\u5927\u540c\u5340\u627f\u5fb7\u8def\u4e00\u6bb5\u4e00\u865f",
            "city": "Taipei",
            "country": "Taiwan",
            "zip": "103",
            "latitude": 25.049230908176,
            "longitude": 121.51719644493
         },
         "id": "195588960470358"
      },
   ]
}
```

#### 以 place id 查詢詳細資料

```
https://graph.facebook.com/195588960470358
```

取得

```
{
   "id": "195588960470358",
   "name": "\u677f\u6a4b\u6797\u5bb6\u82b1\u5712",
   "picture": "http://profile.ak.fbcdn.net/static-ak/rsrc.php/v2/yN/r/cDKJtvtlYv5.png",
   "link": "http://www.facebook.com/pages/\u0025E6\u00259D\u0025BF\u0025E6\u0025A9\u00258B\u0025E6\u00259E\u002597\u0025E5\u0025AE\u0025B6\u0025E8\u00258A\u0025B1\u0025E5\u00259C\u002592/195588960470358",
   "likes": 141,
   "category": "Local business",
   "is_published": true,
   "is_community_page": true,
   "location": {
      "street": "\u65b0\u5317\u5e02\u677f\u6a4b\u5340\u897f\u9580\u88579\u865f",
      "city": "Taipei",
      "country": "Taiwan",
      "zip": "22056",
      "latitude": 25.011011386339,
      "longitude": 121.45513650235
   },
   "phone": "02-2965-3061",
   "checkins": 2745,
   "talking_about_count": 106
}
```

`checkins` 為打卡數
