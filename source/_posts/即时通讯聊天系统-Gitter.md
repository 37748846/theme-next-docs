---
abbrlink: ''
categories: []
date: Mon, 03 Apr 2023 05:10:00 GMT
excerpt: Gitter Gitter是GitHub存储库的开发人员和用户的 即时通讯聊天室系统。 Gitter 作为软件即服务提供商,提供包括免费选项和所有基本功能,以及创建单个私人聊天室的能力,和个人和组织的...
tags:
- Gitter
- 即时通讯]
- Hexo
title: 即时通讯聊天系统-Gitter
updated: Mon, 03 Apr 2023 05:10:00 GMT
---
# Gitter

```
Gitter是GitHub存储库的开发人员和用户的
```

[即时通讯](https://cloud.tencent.com/product/im?from=20065&from_column=20065)聊天室系统。 Gitter 作为软件即服务提供商,提供包括免费选项和所有基本功能,以及创建单个私人聊天室的能力,和个人和组织的付费订阅选项,允许他们创建任意数量的私人聊天室。

```
该服务可以为 GitHub 上的各个 Git 存储库创建个人聊天室(其隐私性遵循关联 GitHub 存储库的隐私设置),用户也可以通过 GitHub 登录 Gitter 访问的存储库的私人聊天室。
```


```
```

[gitter客户端下载](https://gitter.im/home)

## Gitter使用

> 使用说明

1.访问[Gitter官网](https://gitter.im/)并注册用户,目前支持GitLab、GitHub、Twitter 三种方式

2.创建[社区-community](https://gitter.im/gitter/gitter#createcommunity),[创建Gitter聊天室](https://gitter.im/gitter/gitter#createroom),输入聊天室名称并可选择github仓库(如果是私有仓库需进行授权),可设定聊天室权限

3.集成gitter到个人网站,可借助Sidecar等直接集成

```javascript
# sidecar
<script>
((window.gitter = {}).chat = {}).options = {
	room: '[communityName]/community'
};
</script>
<script src="https://sidecar.gitter.im/dist/sidecar.v1.js" async defer></script>
```

复制

> 扩展应用

```
基于上述场景应用,可参考
```

[hexo-gitter](https://github.com/wshunli/hexo-gitter)将其嵌入到个人网站

```javascript
'use strict';
hexo.extend.helper.register("gitter", function (room) {
    return "<script>((window.gitter = {}).chat = {}).options = { room: '" + room + "'};</script>"
        + "<script src=\"https://sidecar.gitter.im/dist/sidecar.v1.js\" async defer></script>";
});
```

复制

```
针对很多主题已经嵌入了gitter相关内容,只需要开启配置开关和Gitter所需参数即可。以butterfly主题为例,其已整合了gitter引入,具体参考源码
```

`themes/butterfly/layout/includes/third-party`,该目录下存放了第三方组件相关的内容,`chat/gitter.pug`则对应为gitter引入相关的内容,其构建核心也是基于`sidecar`
