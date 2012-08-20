---
layout: post
title: "我的 Sublime 設定"
date: 2012-08-19 21:46
comments: true
categories: ["IDE", "Sublime"]
---
<a href="http://www.sublimetext.com/" target="_blank">Sublime</a> 是一套強大的文字編輯器，和我用過的 Textmate 類似，但功能更為強大 XD 以下是我的設定範例。

<!-- more -->

### 實用快速鍵

參考: <a href="https://gist.github.com/1207002" target="_blank">https://gist.github.com/1207002</a>

### 安裝好後設定 link

```
ln -s "/Applications/Sublime Text 2.app/Contents/SharedSupport/bin/subl" ~/bin/subl

sudo ln -s "/Applications/Sublime Text 2.app/Contents/SharedSupport/bin/subl" /usr/bin/subl
```

`ubuntu` 的話可以用

```
sudo ln -s ~/Applications/Sublime\ Text\ 2/sublime_text /usr/bin/subl
```

shell link，編輯 `~/.bashrc` 或 `~/.zshrc` (看你用的是哪一個 shell)

```
export EDITOR="/usr/local/bin/subl -w"
```


### setting 檔 

`command + ,` 出現的檔案  

```
{
	"color_scheme": "Packages/Color Scheme - Default/Twilight.tmTheme",
	"font_size": 18.0,
	"tab_size": 2,
	"translate_tabs_to_spaces": true,  
}

```

(PS: 年紀大了所以用字體 size 用 18 XD)  
(PS2: tab 設定隨個人/公司喜好)


### 安裝 sass/scss 外掛

記得先關掉 Sublime

```
cd ~/Library/Application\ Support/Sublime\ Text\ 2/Packages/
git clone git://github.com/n00ge/sublime-text-haml-sass.git
mv sublime-text-haml-sass/Ruby\ Haml ./
mv sublime-text-haml-sass/SASS ./
rm -rf sublime-text-haml-sass
```

更多可參考<a href="https://github.com/n00ge/sublime-text-haml-sass" target="_blank">官方連結</a>  

### 安裝 ERB 檔案的編輯強化

```
git clone git@github.com:eddorre/SublimeERB.git ~/.sublime_erb

ln -fs ~/.sublime_erb/erb_block.py ~/Library/Application\ Support/Sublime\ Text\ 2/Packages/User
```

"Sublime Text 2" > Preferences > "Key Binding User"  
在 `[` 和 `]` 間貼上

```
{ "keys": ["ctrl+shift+."], "command": "erb", "context":
    [
      { "key": "selector", "operator": "equal", "operand": "text.html.ruby, text.haml, source.yaml, source.css, source.scss, source.js, source.coffee" }
    ]
}
```

在 .erb 檔案中使用 `Ctrl+Shift+.` 就可以出現 `<%= %>` 的內容，重複按就可以切換不同用途的 erb block  
  
更多可參考<a href="https://github.com/eddorre/SublimeERB" target="_blank">官方文件</a>

### 安裝 RubyTest

透過快速鍵跑單一檔案的 `rspec` (或 rails 內建的 test)

```
cd ~/Library/Application\ Support/Sublime\ Text\ 2/Packages/
git clone https://github.com/maltize/sublime-text-2-ruby-tests.git RubyTest
cp RubyTest/RubyTest.sublime-settings ./User
```

修改 `RubyTest.sublime-settings`  
  
`vim User/RubyTest.sublime-settings`
  
內容如下

```
{
  "erb_exec": "bundle exec erb",
  "ruby_unit_exec": "bundle exec ruby",
  "ruby_cucumber_exec": "bundle exec cucumber -f pretty",
  "ruby_rspec_exec": "bundle exec rspec",

  "ruby_unit_folder": "test",
  "ruby_cucumber_folder": "features",
  "ruby_rspec_folder": "spec",

  "ruby_use_scratch" : false,
  "save_on_run": false,
  "ignored_directories": [".git", "vendor", "tmp"]
}
```

主要是在 `rspec` 的命令前加上 `bundle exec`  
  
`Command-Shift-R` 即可直接執行該檔案的 `rspec`，更多可參考 <a href="https://github.com/maltize/sublime-text-2-ruby-tests" target="_blank">官方文件</a>  
  
<a href="http://bit.ly/Nd1TjR" target="_blank">rvm issue</a> 目前尚未解決，預設會用 rvm default 的 ruby 版本，有人<a href="http://bit.ly/Nd2dz7" target="_blank"> hack 了</a>，但不是很喜歡 :p  


