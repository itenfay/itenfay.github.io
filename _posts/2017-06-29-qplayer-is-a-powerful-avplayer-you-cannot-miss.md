---
layout: post
title: "iOS 定制自己的直播/音视频播放器 QPlayer"
header-img: "images/qplayer/post-bg-qplayer.png"
author: "dgynfi"
date: 2017-06-29
tag: iOS
---

`QPlayer`是一款你不容错过的强大的音视频播放器，支持 `M4V、WMV、MP4、MOV、AVI、MKV、MPEG、MPG、FLV、RM、RMVB、MP3` 等主流媒体格式。输入任何 `HTTP、RTMP、RTSP、HLS` 地址播放网络流或直播。`QPlayer` 使用了 `ffmpeg`，支持 `WiFi` 文件传输。聚合了多个直播、视频和短视频平台，可在线观看直播、视频和短视频。

## 免责声明

本文使用的相关技术以及资源仅用于学习交流，请勿用于其他任何商业用途，否则后果自负！！！

部分资源和内容来源于互联网，如有涉及版权和法律法规问题，就请提 issue 或发送 email 联系本人。在核实确认后，本人会将其删除。

## QQ群 (ID:614799921)

![614799921](https://dgynfi.github.io/images/qrcode/g614799921.jpg)

## 预览

-  本地视频

![local_videos.png](https://dgynfi.github.io/images/qplayer/local_videos.png)

- 直播和电视

![ms_live.png](https://dgynfi.github.io/images/qplayer/ms_live.png)

![live_tv.png](https://dgynfi.github.io/images/qplayer/live_tv.png)

- 网页视频

![web_videos.png](https://dgynfi.github.io/images/qplayer/web_videos.png)

- 应用介绍

![app_intro.png](https://dgynfi.github.io/images/qplayer/app_intro.png)

## 要求

iOS 8.0+, iPhone and iPad, Xcode10+.

## 文件传输

在 `App` 设置中打开 `WiFi` 文件传输的开关，即可享用 WiFi 文件传输服务。在电脑浏览器中输入，例如：“[http://192.168.6.6:8888](http://192.168.6.6:8888)”，打开网页后，选择文件，点击 `Upload` 上传。在上传媒体文件时，确保电脑和手机在同一 `WiFi` 环境并且不要关闭本应用也不要锁屏。

PS：建议使用PC浏览器（ Safari [Mac], Microsoft Edge [Win10], Google Chrome [Mac Win10] ）

## 开源组件

- [AFNetworking](https://github.com/AFNetworking/AFNetworking)

  A delightful networking framework for iOS, macOS, watchOS, and tvOS. 

- [SDWebImage](https://github.com/SDWebImage/SDWebImage)

  This library provides an async image downloader with cache support. For convenience, we added categories for UI elements like UIImageView, UIButton, MKAnnotationView.

- [ijkplayer](https://github.com/bilibili/ijkplayer) 

  Android/iOS video player based on FFmpeg n3.4, with MediaCodec, VideoToolbox support.  [[ IJKMediaFramework.framework Download ]](https://pan.baidu.com/s/1WCZzdCUiaQL3a1yJSD22QQ) (提取码: mxqq)

- [MBProgressHUD](https://github.com/jdg/MBProgressHUD)

  MBProgressHUD is an iOS drop-in class that displays a translucent HUD with an indicator and/or labels while work is being done in a background thread. The HUD is meant as a replacement for the undocumented, private UIKit UIProgressHUD with some additional features.

- [MJRefresh](https://github.com/CoderMJLee/MJRefresh)

  An easy way to use pull-to-refresh.

- [FDFullscreenPopGesture](https://github.com/forkingdog/FDFullscreenPopGesture) 

  A UINavigationController's category to enable fullscreen pop gesture with iOS7+ system style.

- [PYSearch](https://github.com/ko1o/PYSearch)

   An elegant search controller which replaces the UISearchController for iOS (iPhone & iPad) .

- [CocoaWebResource](https://github.com/robin/cocoa-web-resource)

  A file transfer solution for iPhone and iPod Touch. Support uploading, download and delete files via browser.

- [ZFPlayer](https://github.com/renzifeng/ZFPlayer) 

  Support customization of any player SDK and control layer(支持定制任何播放器SDK和控制层)

- [KSYMediaPlayer_iOS](https://github.com/ksvc/KSYMediaPlayer_iOS)

  金山云iOS播放SDK（KSYUN Live Streaming player SDK），支持RTMP HTTP-FLV HLS 协议（supporting RTMP HTTP-FLV HLS protocol）。与系统播放器MPMoviePlayerController接口一致，可以无缝快速切换至KSYMediaPlayer；本地全媒体格式支持, 并对主流的媒体格式(mp4, avi, wmv, flv, mkv, mov, rmvb 等 )进行优化；支持广泛的流式视频格式, HLS, RTMP, HTTP Rseudo-Streaming 等；低延时直播体验，配合金山云推流sdk，可以达到全程直播稳定的4秒内延时；实现快速满屏播放，为用户带来更快捷优质的播放体验；支持画面旋转，音量调节等各种功能。

- [SVBlurView](https://github.com/TransitApp/SVBlurView)

  SVBlurView is a simple reimplementation of FXBlurView for iOS 7. It uses Apple's UIImage+ImageEffects category as well as the new drawViewHierarchyInRect: UIView API. It doesn't do dynamic blurs yet.

- [MBProgressHUD-JDragon](https://github.com/lyc59621/MBProgressHUD-JDragon)

  封装MBProgressHUD的一个类别。

## 最后

附上 `QPlayer` 源码地址，觉得还行呢，麻烦顺手给个`star`。适配了深色模式，在 App 设置里添加深色与浅色模式切换开关，开启后会自动跟随系统设置。喜欢 `iOS` 音视频开发的小伙伴，希望能够解决你的一些问题。即使不能，也希望能给你提供一些思路！！！

源码地址 =>【[https://github.com/dgynfi/QPlayer](https://github.com/dgynfi/QPlayer)】
