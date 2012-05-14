---
layout: post
title: "巢狀 (nested) to_json / to_xml 應用"
date: 2011-12-23 21:40
comments: true
author: MarsZ
categories: ["JSON","XML","ActiveRecord"]
---

在做 api 輸出(json,xml等)時, 想要將 model 中的某些 attribute 或關連的其他 model 一起輸出, rails 中的 to_json 或 to_xml 更可以針對 method 做輸出!

<!-- more -->

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
