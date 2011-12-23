---
layout: post
title: "巢狀 (nested) to_json / to_xml 應用"
date: 2011-12-23 21:40
comments: true
categories: [JSON,XML,ActiveRecord]
---

## 參考

* ApiDock

<a href="http://apidock.com/rails/ActiveRecord/Serialization/to_json" target="_blank">http://apidock.com/rails/ActiveRecord/Serialization/to_json</a>

* Stackoverflow

<a href="http://stackoverflow.com/questions/4443218/ror-nested-include-to-include-sub-resources-in-to-xml-to-json" target="_blank">http://stackoverflow.com/questions/4443218/ror-nested-include-to-include-sub-resources-in-to-xml-to-json</a>

## 說明

* 參數為 include
* 可以搭配 only, methods 做部分 attribute 或 method 的輸出

## 範例

```ruby
foo.to_json(:include => 
               { :emails => { :only => :address }, 
	             :friends => { :only => :name, 
		           :include => { :emails => { :only => :address } } 
		         }
		       })
```