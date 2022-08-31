---
layout: post
title: "iOS Crash 闪退日志详细解析教程"
author: "chenxing"
date: 2017-10-15
tag: iOS
---


### 前言

查看闪退日志的步骤：
（1）获取闪退日志
（2）获取 symbolicatecrash 脚本
（3）获取闪退日志对应的符号表（.dSYM 文件）
（4）解析闪退日志

### 苹果官网关于应用程序崩溃的介绍

[https://developer.apple.com/library/content/technotes/tn2151/_index.html](https://link.jianshu.com?t=https://developer.apple.com/library/content/technotes/tn2151/_index.html)

> Understanding and Analyzing Application Crash Reports

> When an application crashes, a crash report is created which is very useful for understanding what caused the crash. This document contains essential information about how to symbolicate, understand, and interpret crash reports.

理解和分析应用程序崩溃报告：

当应用程序崩溃时，会创建一个崩溃报告，这对于理解导致崩溃的原因非常有用。本文档包含有关如何符号化、理解和解释崩溃报告的基本信息。

### 一、获取iOS 设备上闪退日志

![crash_flow.png](https://chenxing640.github.io/images/crashreport/crash_flow.png)

1、(windows 电脑) 使用爱思助手获取闪退日志将 iOS 设备和 windows 电脑连接，打开爱思助手，查看闪退日志，将闪退日志导出，导出后会得到一个后缀为ips的文件。修改后缀名为 xxx.crash。
2、(Mac 电脑) 将设备连接到电脑，进入  ~/Library/Logs/CrashReporter/MobileDevice 文件夹可以获得闪退日志。
3、通过 Xcode 获取闪退日志：打开 Xcode > Window > Devices and Simulators > Devices 标签 > View Device Logs 就可以查看闪退日志。
4、获取已经发布到 appStore 和上传到 testflight 的 app，在用户和测试员设备上发生的闪退日志。打开 Xcode > Window > Organizer 选择 Crashes 标签。然后我们就可以看见一个闪退日志列表。选中某一个闪退日志右键显示包内容，然后进入 DistributionInfos > all > logs。就可以看见闪退日志了。(⚠️ 如果发布时 .dSYM 文件也上传到了 Apple 的服务器，一般点击 Open In Project 就会直接定位到项目闪退的位置)。

苹果官网关于发布到市场和 testflight 上的闪退日志的介绍

[https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppDistributionGuide/AnalyzingCrashReports/AnalyzingCrashReports.html](https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppDistributionGuide/AnalyzingCrashReports/AnalyzingCrashReports.html) 

### 二、闪退日志的组成

```
{"app_name":"DYFStore","timestamp":"2020-06-04 11:49:39.00 +0800","app_version":"1.0.4","slice_uuid":"ce8411b3-9e3d-37b5-af23-391f1950731e","adam_id":0,"build_version":"12","bundleID":"com.xxx.szj","share_with_app_devs":0,"is_first_party":0,"bug_type":"109","os_version":"iPhone OS 13.5 (17F75)","incident_id":"4F095ADE-AD81-42A8-8AC6-C866A986E598","name":"DYFStore"}
Incident Identifier: 4F095ADE-AD81-42A8-8AC6-C866A986E598
CrashReporter Key:   eab392d207f14df9653fda5b6cfceec667d3dd9e
Hardware Model:      iPhone10,2
Process:             DYFStore [5924]
Path:                /private/var/containers/Bundle/Application/1822D267-C7CD-4320-AAD1-EB4CC9657B07/DYFStore.app/DYFStore
Identifier:          com.hncs.szj
Version:             12 (1.0.4)
Code Type:           ARM-64 (Native)
Role:                Foreground
Parent Process:      launchd [1]
Coalition:           com.xxx.szj [2094]


Date/Time:           2020-06-04 11:49:39.5229 +0800
Launch Time:         2020-06-04 11:49:28.4710 +0800
OS Version:          iPhone OS 13.5 (17F75)
Release Type:        User
Baseband Version:    5.60.01
Report Version:      104

Exception Type:  EXC_CRASH (SIGABRT)
Exception Codes: 0x0000000000000000, 0x0000000000000000
Exception Note:  EXC_CORPSE_NOTIFY
Triggered by Thread:  1

Last Exception Backtrace:
(0x19624d794 0x195f6fbcc 0x1967251fc 0x1965284a0 0x19a6d7040 0x19a6d6df4 0x19a7937b0 0x19a7936c4 0x19a7a1e00 0x1027af060 0x1027aeb7c 0x1027ab724 0x10279e5c8 0x1027670b0 0x102766704 0x10277661c 0x1027766f4 0x1a4e2952c 0x195f129a8 0x195f13524 0x195eecf38 0x195ef9534 0x195ef9cd0 0x195f64b38 0x195f67740)

Thread 0 name:  Dispatch queue: com.apple.main-thread
Thread 0:
0   libsystem_kernel.dylib            0x0000000196021198 0x19601d000 + 16792
1   libsystem_kernel.dylib            0x000000019602060c 0x19601d000 + 13836
2   CoreFoundation                    0x00000001961cb468 0x196123000 + 689256
3   CoreFoundation                    0x00000001961c649c 0x196123000 + 668828
4   CoreFoundation                    0x00000001961c5ce8 0x196123000 + 666856
5   GraphicsServices                  0x00000001a031038c 0x1a030d000 + 13196
6   UIKitCore                         0x000000019a2f4444 0x1998c6000 + 10675268
7   DYFStore                          0x0000000102795a3c 0x102760000 + 219708
8   libdyld.dylib                     0x000000019604d8f0 0x19604c000 + 6384

Thread 1 name:  Dispatch queue: com.apple.root.default-qos
Thread 1 Crashed:
0   libsystem_kernel.dylib            0x0000000196042d88 0x19601d000 + 155016
1   libsystem_pthread.dylib           0x0000000195f5f74c 0x195f59000 + 26444
2   libsystem_c.dylib                 0x0000000195eae934 0x195e3c000 + 469300
3   libc++abi.dylib                   0x0000000196016cc0 0x196004000 + 76992
4   libc++abi.dylib                   0x0000000196008e10 0x196004000 + 19984
5   libobjc.A.dylib                   0x0000000195f6fe80 0x195f6a000 + 24192
6   libc++abi.dylib                   0x000000019601614c 0x196004000 + 74060
7   libc++abi.dylib                   0x00000001960160e4 0x196004000 + 73956
8   libdispatch.dylib                 0x0000000195f13538 0x195eb8000 + 374072
9   libdispatch.dylib                 0x0000000195eecf38 0x195eb8000 + 216888
10  libdispatch.dylib                 0x0000000195ef9534 0x195eb8000 + 267572
11  libdispatch.dylib                 0x0000000195ef9cd0 0x195eb8000 + 269520
12  libsystem_pthread.dylib           0x0000000195f64b38 0x195f59000 + 47928
13  libsystem_pthread.dylib           0x0000000195f67740 0x195f59000 + 59200
```

| 关键字 | 含义 |
|:-------:|:-----:|
| Incident Idnetifier | 闪退的唯一标示 |
| CrashReporter Key | 设备标识相对应的唯一键值 (并非真正的设备的 UDID，苹果为了保护用户隐私 iOS6 以后已经无法获取)。通常同一个设备上同一版本的 app 发生 Crash 时，该值都是一样的。 |
| Hardware Model | 设备类型 |
| Version | 当前 app 的版本号，由 Info.plist 中的两个字段组成，CFBundleShortVersionString and CFBundleVersion |
| Code Type |  当前 app 的 CPU 架构 |
| Exception Types | 异常类型 |
| Exception Subtype | 异常子类型 |

**⚠️ 每次打包的时候，一定要确保 Version 的唯一性。**

| 异常 | 描述 | 注释 |
|:-----:|:----:|:-----:|
| EXC_BAD_ACCESS | Bad Memory Access | Access to “bad” memory address. The “bad” can be either “the address does not exist” or “the app does not have privilege to access to”. So it usually ties to SIGBUS and SIGSEGV. |
| EXC_CRASH | Abnormal Exit | Usually tie to SIGABRT, meaning the app exits abnormally by detecting some uncaught exceptions through the code. |
| EXC_BREAKPOINT | Trace / breakpoint Trap | Usually tie to SIGTRAP. Can be triggered either by your own code or NSExceptions being thrown. |
| EXC_GUARD | Violated Guarded Resource Protection | Be triggered by violating a guarded resource protection, like “certain file descriptor”. |
| EXC_BAD_INSTRUCTION | Illegal Instruction | Usually related to certain illegal or undefined instruction or operand. |
| EXC_RESOURCE | Resource Limit | App crashes by hitting resource consumption limit. |
| 00000020 | Hexadecimal Exception Type | Not “OS Kernel” exception. |

参考文章
[http://www.5neo.be/understanding-ios-exception-types/](http://www.5neo.be/understanding-ios-exception-types/)

### 三、符号化闪退日志

1、获取 symbolicatecrash 工具

打开终端输入下面的命令

```
find /Applications/Xcode.app -name symbolicatecrash -type f
```

或者

```
find /Applications/Xcode.app -name symbolicatecrash
```

我们会得到这样一个路径:

> /Applications/Xcode.app/Contents/Developer/Platforms/AppleTVSimulator.platform/Developer/Library/PrivateFrameworks/DVTFoundation.framework/symbolicatecrash

打开 finder 前往这个路径

![symbolicatecrash.png](https://chenxing640.github.io/images/crashreport/symbolicatecrash.png)

拷贝 symbolicatecrash

2、获取 .dSYM 文件

打开 Xcode 选择 Window > Organizer > 选择 Archives 标签。我们可以看到我们打完包的一个列表。我们可以看见闪退日志的信息里面有这样一个版本号的信息 Version: 12 (1.0.4)，找到打包列表里面和这个 build 号相对应的 .xcarchive 文件，然后右键 Show In Finder。

显示包内容，进入dSYMs 文件夹 > 拷贝项目相应的 .dSYM 文件

3、将 .crash 文件和拷贝出来的 symbolicatecrash 脚本以及闪退日志对应的 .dSYM 文件拷贝到同一个文件夹里面。然后打开终端进入这个文件夹。

![cr.png](https://chenxing640.github.io/images/crashreport/cr_required_files.png)

打开终端输入以下命令：

```
 cd /Users/xxx/Desktop/CrashReport/
```

注释：cd /Users/你电脑的用户名/存放上述三个文件的文件夹路径

在终端输入 `vim .bash_profile `，如下图所示：

![vim_bash_profile.png](https://chenxing640.github.io/images/crashreport/vim_bash_profile.png)

添加`export DEVELOPER_DIR="/Applications/XCode.app/Contents/Developer"`，如下图所示，退出并保存。

![export_developer_dir.png](https://chenxing640.github.io/images/crashreport/export_developer_dir.png)

然后输入

```
./symbolicatecrash DYFStore-114939.crash DYFStore.app.dSYM > vcrashLog.txt 
```

等命令执行完成之后，我们可以在刚才的文件夹下面发现 vcrashLog.txt 符号化后的闪退日志。

 1、[DYFStore](https://link.jianshu.com?t=https://github.com/chenxing640/DYFStore)-114939.crash -- 闪退日志
 2、DYFStore.app.dSYM -- dSYM 文件
 3、vcrashLog.txt -- 符号化后的日志

我们可以在已经符号化后的闪退日志里面看到我们代码出错的地方。(可以直接定位到闪退发生在哪个文件的哪一行代码)

![vcrashLog.png](https://chenxing640.github.io/images/crashreport/vcrashLog.png)

---
