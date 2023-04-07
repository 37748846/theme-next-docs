---
abbrlink: ''
categories: []
date: Fri, 07 Apr 2023 15:38:28 GMT
excerpt: ' 查看用户名 :git config user.name 查看密码: git config user.password 查看邮箱:git config
  user.email 查看配置信息: \$ gi...'
tags:
- git
- 用户名
- 密码
- 邮箱
- 查看
- 命令
title: 查看git的用户名和密码
updated: Fri, 07 Apr 2023 15:38:28 GMT
---

> 查看用户名 :git config user.name
>
> 查看密码: git config user.password
>
> 查看邮箱:git config user.email
>
> 查看配置信息: \$ git config --list

> 修改用户名
> git config --global user.name "xxxx(新的用户名)"
>
> 修改密码
> git config --global user.password "xxxx(新的密码)"
>
> 修改邮箱
> git config --global user.email "xxxx@xxx.com(新的邮箱)"

**修改报错:**

![](https://img-blog.csdnimg.cn/img_convert/211622dd7ec2bb67e57a54dfb7b92887.png)

原因:用户名过多

![](https://img-blog.csdnimg.cn/img_convert/7ecbce660287a1065fac968dc6664681.png)

>   解决办法:\$ git config --global --replace-all user.name "你的 git 的名称"
>
>         \$ git config --global --replace-all uesr.email "你的 git 的邮箱"

![](https://img-blog.csdnimg.cn/img_convert/ba1e100ffd07c5ab4214239e0a6d7684.png)
