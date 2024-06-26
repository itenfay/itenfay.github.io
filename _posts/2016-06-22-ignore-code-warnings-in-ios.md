---
layout: post
title: "消除 iOS 代码中的警告"
author: "Teng Fei"
date: 2016-06-22
tag: iOS
---

## 消除 iOS 代码中的警告

在iOS开发过程中，我们可能会碰到一些警告，例如：系统弃用方法，没有实现的selector等一些警告。对于有强迫症的程序猿来说，十分不能忍受，那么我们今天就来解决它吧！

- 基本语法

```
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-相关命令"

// 要消除警告的代码

#pragma clang diagnostic pop
```

### 常见的几种情形

- 未实现某个方法 `-Wundeclared-selector`

```
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wundeclared-selector"

// 要消除警告的代码
YYDog *dog = [[YYDog alloc] init];
[dog performSelector:@selector(eat)];

#pragma clang diagnostic pop
```

- performSelector `-Warc-performSelector-leaks`

```
#pragma clang diagnostic push     
#pragma clang diagnostic ignored "-Warc-performSelector-leaks"     
// 要消除警告的代码
#pragma clang diagnostic pop 
```

- 方法弃用警告 `-Wdeprecated-declarations`

```
#pragma clang diagnostic push    
#pragma clang diagnostic ignored "-Wdeprecated-declarations"         

[XX setDeviceIdentifier:[[UIDevice currentDevice] uniqueIdentifier]];    

#pragma clang diagnostic pop  
```

- 不兼容指针类型 `-Wincompatible-pointer-types`

```
#pragma clang diagnostic push     
#pragma clang diagnostic ignored "-Wincompatible-pointer-types"     

#pragma clang diagnostic pop  
```

- 循环引用 `-Warc-retain-cycles`

```
#pragma clang diagnostic push    
#pragma clang diagnostic ignored "-Warc-retain-cycles"    
self.completionBlock = ^ {    
...    
};    
#pragma clang diagnostic pop
```

- 未使用变量 `-Wunused-variable`
```
#pragma clang diagnostic push     
#pragma clang diagnostic ignored "-Wunused-variable"     
int a;     
#pragma clang diagnostic pop 
```

### 什么？没有你碰到的情形？

- [查看相关命令](https://www.jianshu.com/p/b5b58cb5ee38)
