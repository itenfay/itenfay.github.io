---
layout: post
title: "iOS 链式编程之仿真安卓 Toast"
author: "dgynfi"
date: 2019-08-08
tag: iOS
---


做过安卓开发的人都知道 Toast，它会在界面上显示一排黑色背景的文字，用于提示用户信息，但 iOS 并没有类似的控件，所以，今天我就来讲讲在 iOS 上如何仿安卓 Toast？

## 原理

说白了，安卓中的 Toast 可以理解成 iOS 中的一个黑色背景的 `UILabel` 或者 带点击事件的 `UIButton`，并还可以自己设置*背景颜色、文本颜色、位置、边角大小，边框线条宽度和颜色*等等。

说了这么多，如何仿真安卓 Toast 呢？我专门写了一个 `DYFToast` 类，支持**横竖屏切换**，支持**点击移除**，边角处理时避免**离屏渲染**，采用**链式编程**思想，通过**点语法操作**，实现完全类似安卓的 Toast。

## 安装

支持通过 CocoaPods 安装。

```
# Install lastest version
pod 'DYFToast'
```

接下来一起阅读它的使用说明：

## 使用说明

**1. 导入头文件**

```
#import "DYFToast.h"
```

**2. 默认设置并显示**

```
- (IBAction)defaultAction:(id)sender {
    Toast().makeText(self.view, "无效的验证码", Toastl.LENGTH_SHORT).show();
}
```

**3. 设置位置**

```
//  Four: 
//  Gravity.TOP_EDGE, Gravity.TOP
//  Gravity.CENTER, Gravity.BOTTOM
- (IBAction)setGravityAction:(id)sender {
    static int i = 0;

    GravityType type = Gravity.TOP_EDGE;
    char *message = "网络连接超时，请重试";
    if (i == 0) {
        type = Gravity.TOP_EDGE;
        message = "网络连接超时，请重试";
    } else if (i == 1) {
        type = Gravity.TOP;
        message = "请求失败";
    } else if (i == 2) {
        type = Gravity.CENTER;
        message = "清理完成";
    } else if (i == 3) {
        type = Gravity.BOTTOM;
        message = "请输入用户名";
    }

    i++;
    if (i >= 4) { i = 0; }

    UIView *inView = self.navigationController.view;
    
    Toast().makeText(inView, message, Toastl.LENGTH_LONG)
    .setGravity(type)
    .show();
}
```

**4. 设置背景和文本颜色**

```
- (IBAction)setColorAction:(id)sender {
    UIColor *bgColor = [UIColor colorWithRed:120/255.0 green:210/255.0 blue:251/255.0 alpha:0.9];
    UIColor *textColor = [UIColor colorWithRed:255/255.0 green:255/255.0 blue:255/255.0 alpha:1.0];
    
    char *message = "Wrong username and password";
    
    Toast().makeText(self.view, message, Toastl.LENGTH_LONG)
    .setGravity(Gravity.BOTTOM)
    .setColor(bgColor, textColor)
    .show();
}
```

**5. 设置边角**

```
- (IBAction)setCornerAction:(id)sender {
    char *message = "Please input email";
    
    Toast().makeText(self.view, message, Toastl.LENGTH_LONG)
    .setGravity(Gravity.BOTTOM)
    .setCorner(20)
    .show();
}
```

**6. 设置边框**

```
- (IBAction)setBorderAction:(id)sender {
    char *message = "手机号码格式不正确，请重输入";
    
    Toast().makeText(self.view, message, Toastl.LENGTH_LONG)
    .setGravity(Gravity.BOTTOM)
    .setBorder(UIColor.orangeColor, 3)
    .show();
}
```

## 最后

附上 `DYFToast` 项目地址：[https://github.com/dgynfi/DYFToast](https://github.com/dgynfi/DYFToast)，觉得还行呢，麻烦顺手给个star。对开发实现 Toast 或者喜欢研究的小伙伴，希望能够解决你的一些问题。即使不能，也希望能给你提供一些思路。
