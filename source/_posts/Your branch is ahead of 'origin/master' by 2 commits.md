---
abbrlink: ''
categories: []
date: Sat, 08 Apr 2023 11:16:35 GMT
excerpt: '在github上想上传文件时报: Your branch is ahead of ''origin/master'' by 2 commits.
  网上查找原因是:在之前已经有2个[commit]而没有pu...'
tags:
- github
- git
title: Your branch is ahead of 'origin/master' by 2 commits
updated: Sat, 08 Apr 2023 11:16:35 GMT
---
在github上想上传文件时报:

`Your branch is ahead of 'origin/master' by 2 commits.`

网上查找原因是:在之前已经有2个[commit]而没有push到远程分支上

解决方法:

(1)先git push origin \*\*将本地分支提到远程仓库;

(2)或者直接`git reset--hard HEAD\~x`解决,x表示的就是在这之前已经有多少次的提交,这句命令的意思就是直接回退到x 个commit之前(这两次提交所改动的代码都没有了,慎用)

另可执行:

`git reset HEAD^ --soft`         --soft 表示保留当前commit,重新commit

`git reset HEAD^ --hard`       --hard 表示丢弃当前add,重新add、commit
