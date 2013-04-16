---
layout: post
title: "新版的 git (1.7 以上) 在 merge 時預設不開編輯器的設定"
date: 2013-04-09 02:02
comments: true
categories: [Git]
---
打開你的 shell rc 例如 `~/.zshrc` 或 `~/bashrc`  
插入以下  

```
export GIT_MERGE_AUTOEDIT=no
```

<!-- more -->

之後 merge --no-ff 的指令就都會預設用 auto message 做 commit 啦  
如果想要強制編輯時，可以用 --edit 參數  

```
git merge branch_name --no-ff --edit
```

