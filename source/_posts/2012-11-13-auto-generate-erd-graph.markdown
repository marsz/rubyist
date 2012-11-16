---
layout: post
title: "自動化產生 ER 圖的 rubygem"
date: 2012-11-13 05:39
comments: true
categories: [Rubygems, ERD]
---
參考連結:  
1. 官網 : <a href="http://rails-erd.rubyforge.org/" target="_blank">http://rails-erd.rubyforge.org/</a>  
2. 相關教學 : <a href="http://zool.me/rails/2010/12/29/Rails-generate-entity-relationship-diagram-projects/" target="_blank">http://zool.me/rails/2010/12/29/Rails-generate-entity-relationship-diagram-projects/</a>  

<!-- more -->

### 系統需求

MAC OS 請先安裝 `brew`  

### 安裝 Graphviz

##### MAC OS

```
brew install cairo pango graphviz
```

若無法順利安裝可至 <a href="http://www.graphviz.org/Download_macos.php" target="_blank">graphviz for MAC OS download</a> 頁面下載對應的系統，直接打開 finder 以 window 介面安裝

##### Ubuntu

```
sudo aptitude install graphviz
```

### Rails 設定

##### Gemfile

```ruby
group :development do
  gem "rails-erd"
end
```


```
bundle install
```

### 生成 ERD

```
bundle exec rake erd
```

生成的 ERD 會在專案目錄下的 erd.pdf
