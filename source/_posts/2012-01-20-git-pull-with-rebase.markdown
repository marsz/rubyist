---
layout: post
title: "git pull 時請愛用 rebase"
date: 2012-01-20 19:44
comments: true
categories: ["git", "git-rebase"] 
---
因為
git pull = git fetch + git merge (remote branch merge to local branch)
而 merge 往往會造成線圖上產生無謂的分支 (可參考 <a href="http://ihower.tw/blog/archives/3843" target="_blank">ihower 大大的 rebase 文章</a> )

<!-- more -->

因此除了一般的 feture branch merge 時透過 rebase 外  
我們對內也會要求 pull 時用 rebase

```
git pull --rebase
```

若覺得每次加都很麻煩可以在專案的 .git/config 加上

```ruby .git/config
[branch "master"]
  remote = origin
  merge = refs/heads/master
  rebase = true # 這行
```

若覺得每個專案都要加很麻煩, 可以在 ~/.gitconfig 加上

```ruby  ~/.gitconfig
[branch]
  autosetuprebase = always
```

