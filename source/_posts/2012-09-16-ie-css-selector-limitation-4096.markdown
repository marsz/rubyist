---
layout: post
title: "IE 瀏覽器讀取 css 檔案的 selector (4096)限制"
date: 2012-09-16 23:24
comments: true
categories: ["stupid IE", "CSS"]
---
IE 瀏覽器在讀取每個 css 檔案時，不能超過 4096 個 selector，超過的都會被忽略
<!-- more -->
註: `.foo .bar { .... }` 代表一個 selector  
`stackoverflow` 也有相關問題: <a href="http://bit.ly/QiuvNC" target="_blank">http://bit.ly/QiuvNC</a>  
  
由於 `twitter boostrap` 以及其應用外掛的流行，要破 4096 個 selector 是輕而易舉的事，要測試你所使用的瀏覽器有沒有支援超過，可以<a href="http://marc.baffl.co.uk/browser_bugs/css-selector-limit/" target="_blank">看這個網頁</a>，若顯示的背景為紅色代表不支援，綠色表示沒問題。  
  
解決此問題最快的辦法就是把 css 分檔案就行了，rails 的 assets precompile 的 css 中若混雜著 bootstrap 之類的套件就會很容易超過此限制，只要把這些不會被修改的套件掛上 CDN 或分不同檔案 precompile 就可以處理了。
