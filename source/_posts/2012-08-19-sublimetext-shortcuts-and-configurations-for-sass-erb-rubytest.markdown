---
layout: post
title: "我的 Sublime 設定"
date: 2012-08-19 21:46
comments: true
categories: ["Sublime"]
---
最後更新: 2013/3/16   
<a href="http://www.sublimetext.com/" target="_blank">Sublime</a> 是一套強大的文字編輯器，和我用過的 Textmate 類似，但功能更為強大 XD  
以下是我的設定範例。

<!-- more -->

實用快速鍵
=========

參考: <a href="https://gist.github.com/1207002" target="_blank">https://gist.github.com/1207002</a>

自定快速鍵
=========

以下視個人喜好而定:  

上方選單 -> Sublime Text 2 -> Preferences -> Key Bindings Default  

```
  // 找 swap_line_up 出來改, 可以換置上下行
  { "keys": ["alt+up"], "command": "swap_line_up" },
  { "keys": ["alt+down"], "command": "swap_line_down" },
  // 找 duplicate_line 出來改, 可以快速複製一行
  { "keys": ["super+alt+down"], "command": "duplicate_line" },
  { "keys": ["super+alt+up"], "command": "duplicate_line" },
  // 找 auto_complete 出來改
  { "keys": ["super+."], "command": "auto_complete" },
```

安裝好後設定 link
================

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


setting 檔 
==========

`command + ,` 出現的檔案  

```
{
	"color_scheme": "Packages/Color Scheme - Default/Twilight.tmTheme",
	"font_size": 18.0,
	"tab_size": 2,
	"translate_tabs_to_spaces": true,  
  "highlight_line": true,
  "highlight_modified_tabs": true
}

```

(PS: 年紀大了所以用字體 size 用 18 XD)  
(PS2: tab 設定隨個人/公司喜好)



安裝 package control
====================

用於快速安裝其他 package 的好工具

參考 <a href="http://wbond.net/sublime_packages/package_control/installation" target="_blank">http://wbond.net/sublime_packages/package_control/installation</a>  
ctrl+` 開啟 command line 後輸入:   

```
import urllib2,os; pf='Package Control.sublime-package'; ipp=sublime.installed_packages_path(); os.makedirs(ipp) if not os.path.exists(ipp) else None; urllib2.install_opener(urllib2.build_opener(urllib2.ProxyHandler())); open(os.path.join(ipp,pf),'wb').write(urllib2.urlopen('http://sublime.wbond.net/'+pf.replace(' ','%20')).read()); print 'Please restart Sublime Text to finish installation'
```

重啟 sublime, `command+shift+p` 輸入 install 看到 `Package Control: Install Package` 即可透過此功能安裝其他 package

安裝 sass/scss/haml 檔案支援
==========================

1. 安裝 `package control`  
2. `command+shift+p` 輸入 `install` 選 `Package Control: Install Package`  
3. 鍵入 `sass` 選第一個安裝即可
4. 重複 `2.`, 鍵入 `haml` 選第一個安裝即可

安裝 ERB 檔案的編輯強化
====================

1. 安裝 `package control`  
2. `command+shift+p` 輸入 `install` 選 `Package Control: Install Package`  
3. 鍵入 `erb snippets` 選第一個安裝即可

參考<a href="https://github.com/matthewrobertson/ERB-Sublime-Snippets" target="_blank">官方文件</a> 有快速 block template 可產生。  

例如在 .erb 檔中，打 `er` 然後再按 `tab` 鍵就會生成 `<% %>`。

