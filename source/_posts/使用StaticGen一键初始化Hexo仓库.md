---
abbrlink: ''
categories: []
date: ''
tags:
- Github
- Netlify
- Hexo
- StaticGen
title: 使用StaticGen一键初始化Hexo仓库
updated: Sun, 02 Apr 2023 14:47:56 GMT
---
# 简要流程

使用Github登陆Netlify。
使用StaticGen一键初始化Hexo仓库。
将Hexo源码仓库Clone到本地,调整网站配置,编写文章。
本地无需Nodejs、NPM、Hexo环境,修改完成后Push到Github,Netlify检测到仓库变更后实现自动部署。
在Netlify整个部署过程中, 你只需要提交代码, 其余的master部署预览(包括MR的预览), HTTPS证书, 静态资源的优化与CDN加速, 部署消息通知, 等等都不用再操心. 真的是太优雅了!
