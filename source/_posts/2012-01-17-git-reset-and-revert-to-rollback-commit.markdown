---
layout: post
title: "透過rebase -i, reset, revert還原某個commit的方法"
date: 2012-01-17 18:56
comments: true
author: MarsZ
categories: ["Git"]
---
個人比較推薦用 reset 或 rebase -i, 底下將會同時介紹 revert 和 reset 的方法

commit 如下  

```
A -> B -> C -> D -> E
```

想要還原到 commit C 之後的狀態 (也就是把 D 和 E rollback)

<!-- more -->

##### 用 git revert

```
git revert E
git revert D
```

結果:

```
A -> B -> C -> D -> E -> F -> G
```

F 是還原 commit E 修改結果的 commit  
G 是還原 commit D 修改結果的 commit  
因此 revert 只會讓 commit 繼續往前  
優點是可以針對某個 commit 進行還原 並且留下還原記錄

##### 用 rebase -i

有關 rebase -i 的詳細使用方式可以參考<a href="http://blog.yorkxin.org/2011/07/29/git-rebase/" target="_blank">這篇</a>  

假如想要抽掉某個 commit 又不想留下記錄, rebase -i 就很好用了  

假如只想要還原 D 變成 A -> B -> C -> E  

```
git rebase -i C
```

這時候會出現文字編輯

```
pick D xxx
pick E ooo
```

把 pick D xxx 整列移除後儲存就可以了
若中間有遇到衝突則必須自行修正後再

```
git add .
git rebase --continue
```

##### 用 reset

用於做整段 commits 的還原  
例如希望還原到 B commit  以後的狀態變成  

```
A -> B  
```

```
git reset B
```

則 git 會將 log 中的 C, D, E 都清除  
但檔案內容沒有任何變動, 因此會看到 C, D, E 修改的檔案處在 unstaged 階段  
若針對部分檔案還原可以用  

```
git checkout [file path]
```

若要全部還原可用

```
git checkout -f
```

#### 結論

還沒 push 前, 個人傾向不產生太多 commit  
因次我都會用 rebase -i 進行編修, 順便合併或reword 一些 commit

某個特殊情況, 例如發現某個 commit 裡面包含了不相干的檔案, 欲重新 commit 時  
就會先用 rebase -i 把欲修改的 commit 換到後面(較新), 然後再用 reset 重新 stage + commit
