---
layout: post
title: "iOS13 快速读懂深色模式 API"
author: "Teng Fei"
date: 2020-05-27
tag: iOS
---

![](https://chenxing640.github.io/images/ios13_dark/iOS13_dark_mode.jpg)

大概一年多以前，`Mac OS` 系统发布了深色模式外观，看着挺刺激，时至今日用着也还挺爽的。

终于，随着 `iPhone11` 等新手机的发售，`iOS 13` 系统也正式发布了，深色模式也出现在了大众视野。

我们这些 `iOS` 程序猿也有事情做了，原有项目适配 `iOS13` 系统，适配深色模式 ( `Dark Mode` )。

> Apps on iOS 13 are expected to support dark mode Use system colors and materials Create your own dynamic colors and images Leverage flexible infrastructure.

提供两种方式设置手机当前外观模式:

- 设置 --> 显示与亮度
- 控制中心，长按亮度调节按钮

## 获取当前模式

我们需要获取到当前处于什么模式，在根据不同的模式进行适配，`iOS 13` 中新增了获取当前模式的 `API`。

- Swift

```
// 获取当前模式
let currentMode = UITraitCollection.current.userInterfaceStyle
if (currentMode == .dark) {
    print("深色模式")
} else if (currentMode == .light) {
    print("浅色模式")
} else {
    print("未知模式")
}

open var userInterfaceStyle: UIUserInterfaceStyle { get } 

public enum UIUserInterfaceStyle : Int {
    // 未指明的
    case unspecified
    // 浅色模式
    case light
    // 深色模式
    case dark
}
```

- Objective-C

```
if (@available(iOS 13.0, *)) {
    UIUserInterfaceStyle mode = UITraitCollection.currentTraitCollection.userInterfaceStyle;
    if (mode == UIUserInterfaceStyleDark) {
        NSLog(@"深色模式");
    } else if (mode == UIUserInterfaceStyleLight) {
        NSLog(@"浅色模式");
    } else {
        NSLog(@"未知模式");
    }
}

typedef NS_ENUM(NSInteger, UIUserInterfaceStyle) {
    UIUserInterfaceStyleUnspecified,
    UIUserInterfaceStyleLight,
    UIUserInterfaceStyleDark,
} API_AVAILABLE(tvos(10.0)) API_AVAILABLE(ios(12.0)) API_UNAVAILABLE(watchos);
```

## 监听系统模式的变化

在 `iOS13` 系统中，`UIViewController` 遵循了两个协议: `UITraitEnvironment` 和`UIContentContainer` 协议

在 `UITraitEnvironment` 协议中，为我们提供了一个监听当前模式变化的方法。

- Swift 

```
public protocol UITraitEnvironment : NSObjectProtocol {
    // 当前模式
    @available(iOS 8.0, *)
    var traitCollection: UITraitCollection { get }

    // 重写该方法监听模式的改变
    @available(iOS 8.0, *)
    func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?)
}

// 使用方法
override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {
    
    super.traitCollectionDidChange(previousTraitCollection)
    
    // 每次模式改变的时候, 这里都会执行
    print("模式改变了")
}
```

- Objective-C

```
@protocol UITraitEnvironment <NSObject>
// 当前模式
@property (nonatomic, readonly) UITraitCollection *traitCollection API_AVAILABLE(ios(8.0));

// 重写该方法监听模式的改变
- (void)traitCollectionDidChange:(nullable UITraitCollection *)previousTraitCollection API_AVAILABLE(ios(8.0));
@end
```

## 推荐阅读

[《iOS13 深色模式与浅色模式适配讲解》](https://chenxing640.github.io/2020/06/01/adaptation-of-dark-mode-and-light-mode-in-iOS13)
