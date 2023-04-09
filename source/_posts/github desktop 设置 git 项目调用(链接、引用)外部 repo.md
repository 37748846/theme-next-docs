---
abbrlink: ''
categories: []
date: Sun, 09 Apr 2023 03:08:14 GMT
excerpt: 0 前言 b站操作视频:https://www.bilibili.com/video/BV1YB4y1g7Jv/ 我们在用github时,常会用到别人的项目,但是每次直接克隆(拷贝)到自己的项目中,会...
tags:
- git
- 项目
title: github desktop 设置 git 项目调用(链接、引用)外部 repo
updated: Sun, 09 Apr 2023 03:08:14 GMT
---
0 前言
b站操作视频:https://www.bilibili.com/video/BV1YB4y1g7Jv/

我们在用github时,常会用到别人的项目,但是每次直接克隆(拷贝)到自己的项目中,会占用太多空间,毕竟github给每个用户的空间是有限的,所以,我们使用引用的方式而不是拷贝的方式来用别人的项目。

看到很多GitHub仓库引用了别人的仓库,就像做了个软链接一样:

点进去打开之后是另一个的GitHub仓库(可以是别人的 repo)。

1 操作流程
已经使用GitHub Desktop克隆了一个项目yolo2via到本地。
在GitHub Desktop中点击Repository --> Open in Command Prompt

然后我们就看到一个终端,如下图

比如我们想要引用yolov7这份项目,在终端输入:

git submodule add https://github.com/WongKinYiu/yolov7.git yolov7

再打开github desktop,输入更新说明,点击Commit to Main,最后点击Fetch origin

再进入github网站,可以看到yolov7添加进去了

2 更多yolo
yolov1
git submodule add https://github.com/pjreddie/darknet.git yolov1
yolov2
git submodule add https://github.com/pjreddie/darknet.git  yolov2
yolov3
git submodule add https://github.com/ultralytics/yolov3.git yolov3
yolov4
git submodule add https://github.com/Tianxiaomo/pytorch-YOLOv4.git yolov4
yolov5
git submodule add https://github.com/ultralytics/yolov5.git yolov5
yolovx
git submodule add https://github.com/Megvii-BaseDetection/YOLOX.git yolovx
yolov6
git submodule add https://github.com/meituan/YOLOv6.git yolov6
yolov7
git submodule add https://github.com/WongKinYiu/yolov7.git yolov7
参考
github - 设置 git 项目调用(链接、引用)外部 repo
GitHub Desktop下添加Submodule
————————————————
版权声明:本文为CSDN博主「CV-杨帆」的原创文章,遵循CC 4.0 BY-SA版权协议,转载请附上原文出处链接及本声明。
原文链接:https://blog.csdn.net/WhiffeYF/article/details/126686686
