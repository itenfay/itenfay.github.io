---
layout: post
title: "iOS一款好用的视频播放器QPlayer"
header-img: "images/qplayer/post-bg-qplayer.png"
author: "chenxing"
date: 2017-06-29
tag: iOS
---


编写这款播放器的目的是将下载好的电影、电视剧和动漫等视频同步到这款APP里观看，节省移动数据流量和避开其他限制。重构了此项目，采用MVP设计模式，提高APP性能，适配APP深浅模式等，使浏览体验更好。


## 特点

- 支持`M4V、WMV、MP4、MOV、AVI、MKV、FLV、RM、RMVB`等主流媒体格式
- 支持`HTTP、RTMP、RTSP、HLS`等流媒体播放
- 支持WiFi传输，可以享用WiFi文件传输服务和本地观看视频
- 支持使用ijkplayer、zfplayer播放视频
- 支持网页动态解析视频，原生播放器播放
- 支持深色和浅色模式自动随系统设置
- 支持画中画播放视频


## 预览

**1. 本地视频**

![PRV_IMG_1739.gif](https://chenxing640.github.io/images/qplayer/PRV_IMG_1739.gif)


## 免责声明

- 本文使用的相关技术以及资源仅用于学习交流，请勿用于其他任何商业用途，否则后果自负！！！
- 部分资源和内容来源于互联网，如有涉及版权和法律法规问题，就请提issue或发送email联系本人。在核实确认后，本人会将其删除。


## QQ群 (ID:614799921)

![614799921](https://chenxing640.github.io/images/qrcode/g614799921.jpg)


## 要求

iOS 11.0+, iPhone and iPad, Xcode14+.


## 文件传输

在`App`设置中打开`WiFi`文件传输的开关，即可享用WiFi文件传输服务。在电脑浏览器中输入，例如：“[http://192.168.6.6:8888](http://192.168.6.6:8888)”，打开网页后，选择文件，点击`Upload`上传。在上传媒体文件时，确保电脑和手机在同一`WiFi`环境并且不要关闭本应用也不要锁屏。

`PS：建议使用PC浏览器（ Safari [Mac], Microsoft Edge [Win10], Google Chrome [Mac Win10] ）`


## 最后

附上`QPlayer`的[源码地址](https://github.com/chenxing640/QPlayer)，如果你觉得还行呢，麻烦顺手给个`star`。
