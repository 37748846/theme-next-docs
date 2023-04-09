---
abbrlink: ''
categories: []
date: Sun, 09 Apr 2023 14:31:38 GMT
excerpt: 更新Next模板的时候网站根本不更新，因为Next模板是用submodule连接的，思考了半天找不到问题所在，网上查了一下应该是缓存问题，可是在Netlify上怎么清。。。
  想到用命令，可是怕网站瘫痪...
tags:
- Netlify
- submodule
- 更新
title: Netlify更新submodule
updated: Sun, 09 Apr 2023 14:38:27 GMT
---
更新Next模板的时候网站根本不更新，因为Next模板是用submodule连接的，思考了半天找不到问题所在，网上查了一下应该是缓存问题，可是在Netlify上怎么清。。。

想到用命令，可是怕网站瘫痪，一直就拖着

最近查资料才找到

Solution

首先请确保自己其它常规操作未出错。

在Deploy failed界面下找到Retry deploy，选择Clear cache and deploy site。
