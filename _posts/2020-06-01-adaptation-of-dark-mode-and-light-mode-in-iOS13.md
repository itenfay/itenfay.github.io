---
layout: post
title: "iOS13 深色模式与浅色模式适配讲解"
author: "Tenfay"
date: 2020-06-01
tag: iOS
---


![iOS 13](https://itenfay.github.io/images/ios13_dark/iOS13_poster.jpg)


2019年6月4日凌晨，苹果在开发者大会上推出了新一代手机操作系统 `iOS 13`，主要更新了照片应用、滑动输入和更多动画表情，还有就是增加了”深色模式“，优化了音量的调节方式。

深色模式”终于来了“。

在所有关于 `iOS13` 的更新项目里，“深色模式”是网友讨论最多的。该模式可以根据日出日落时间自动开启，开启后，不只有壁纸，所有的系统元素都会变成暗色，起到在夜里降低屏幕亮度、保护用户眼睛的作用。

![Light and Dark Mode](https://itenfay.github.io/images/ios13_dark/iOS13_light_dark.jpg)

实际上，苹果用户很早就开始呼吁 `iOS` 加入这个深色模式。如今 `iOS13` 正式发布深色模式，媒体和网友评论的口吻都是“终于来了”，“迟到的深色模式”。

![Dark Mode Apps](https://itenfay.github.io/images/ios13_dark/iOS13_dark_app.jpg)

对于 `iOS` 程序猿们而言，如何适配 `iOS13` 系统的深色模式 ( `Dark Mode` ) 呢？我推荐从以下几个方面去做适配，推荐参考项目 *[QPlayer](https://github.com/itenfay/QPlayer)* 。

### 运用系统 API

如何运用系统 `API`，请阅读这篇文章[《iOS13 快速读懂深色模式 API》](https://itenfay.github.io/2020/05/27/quickly-understand-dark-mode-api-for-iOS13)。

### 颜色相关的适配

- 不同模式的适配主要涉及颜色和图片两个方面的适配。
- 其中颜色适配，包括相关背景色和字体颜色。
- 当系统模式切换的时候，我们不需要如何操作，系统会自动渲染页面，只需要做好不同模式的颜色和图片即可。

**UIColor**

- `iOS13` 之前 `UIColor` 只能表示一种颜色，从 `iOS13` 开始 `UIColor` 是一个动态的颜色，在不同模式下可以分别代表不同的颜色。
- 下面是 `iOS13` 系统提供的动态颜色种类，使用以下颜色值，在模式切换时，则不需要做特殊处理。

```
@interface UIColor (UIColorSystemColors)
#pragma mark System colors

@property (class, nonatomic, readonly) UIColor *systemRedColor          API_AVAILABLE(ios(7.0), tvos(9.0)) API_UNAVAILABLE(watchos);
@property (class, nonatomic, readonly) UIColor *systemGreenColor        API_AVAILABLE(ios(7.0), tvos(9.0)) API_UNAVAILABLE(watchos);
@property (class, nonatomic, readonly) UIColor *systemBlueColor         API_AVAILABLE(ios(7.0), tvos(9.0)) API_UNAVAILABLE(watchos);
@property (class, nonatomic, readonly) UIColor *systemOrangeColor       API_AVAILABLE(ios(7.0), tvos(9.0)) API_UNAVAILABLE(watchos);
@property (class, nonatomic, readonly) UIColor *systemYellowColor       API_AVAILABLE(ios(7.0), tvos(9.0)) API_UNAVAILABLE(watchos);
@property (class, nonatomic, readonly) UIColor *systemPinkColor         API_AVAILABLE(ios(7.0), tvos(9.0)) API_UNAVAILABLE(watchos);
@property (class, nonatomic, readonly) UIColor *systemPurpleColor       API_AVAILABLE(ios(9.0), tvos(9.0)) API_UNAVAILABLE(watchos);
@property (class, nonatomic, readonly) UIColor *systemTealColor         API_AVAILABLE(ios(7.0), tvos(9.0)) API_UNAVAILABLE(watchos);
@property (class, nonatomic, readonly) UIColor *systemIndigoColor       API_AVAILABLE(ios(13.0), tvos(13.0)) API_UNAVAILABLE(watchos);
// 灰色种类, 在Light模式下, systemGray6Color更趋向于白色
@property (class, nonatomic, readonly) UIColor *systemGrayColor         API_AVAILABLE(ios(7.0), tvos(9.0)) API_UNAVAILABLE(watchos);
@property (class, nonatomic, readonly) UIColor *systemGray2Color        API_AVAILABLE(ios(13.0)) API_UNAVAILABLE(tvos, watchos);
@property (class, nonatomic, readonly) UIColor *systemGray3Color        API_AVAILABLE(ios(13.0)) API_UNAVAILABLE(tvos, watchos);
@property (class, nonatomic, readonly) UIColor *systemGray4Color        API_AVAILABLE(ios(13.0)) API_UNAVAILABLE(tvos, watchos);
@property (class, nonatomic, readonly) UIColor *systemGray5Color        API_AVAILABLE(ios(13.0)) API_UNAVAILABLE(tvos, watchos);
@property (class, nonatomic, readonly) UIColor *systemGray6Color        API_AVAILABLE(ios(13.0)) API_UNAVAILABLE(tvos, watchos);

#pragma mark Foreground colors
@property (class, nonatomic, readonly) UIColor *labelColor              API_AVAILABLE(ios(13.0), tvos(13.0)) API_UNAVAILABLE(watchos);
@property (class, nonatomic, readonly) UIColor *secondaryLabelColor     API_AVAILABLE(ios(13.0), tvos(13.0)) API_UNAVAILABLE(watchos);
@property (class, nonatomic, readonly) UIColor *tertiaryLabelColor      API_AVAILABLE(ios(13.0), tvos(13.0)) API_UNAVAILABLE(watchos);
@property (class, nonatomic, readonly) UIColor *quaternaryLabelColor    API_AVAILABLE(ios(13.0), tvos(13.0)) API_UNAVAILABLE(watchos);
// 系统链接的前景色
@property (class, nonatomic, readonly) UIColor *linkColor               API_AVAILABLE(ios(13.0), tvos(13.0)) API_UNAVAILABLE(watchos);
// 占位文字的颜色
@property (class, nonatomic, readonly) UIColor *placeholderTextColor    API_AVAILABLE(ios(13.0), tvos(13.0)) API_UNAVAILABLE(watchos);
// 边框或者分割线的颜色
@property (class, nonatomic, readonly) UIColor *separatorColor          API_AVAILABLE(ios(13.0), tvos(13.0)) API_UNAVAILABLE(watchos);
@property (class, nonatomic, readonly) UIColor *opaqueSeparatorColor    API_AVAILABLE(ios(13.0), tvos(13.0)) API_UNAVAILABLE(watchos);

#pragma mark Background colors
@property (class, nonatomic, readonly) UIColor *systemBackgroundColor                   API_AVAILABLE(ios(13.0)) API_UNAVAILABLE(tvos, watchos);
@property (class, nonatomic, readonly) UIColor *secondarySystemBackgroundColor          API_AVAILABLE(ios(13.0)) API_UNAVAILABLE(tvos, watchos);
@property (class, nonatomic, readonly) UIColor *tertiarySystemBackgroundColor           API_AVAILABLE(ios(13.0)) API_UNAVAILABLE(tvos, watchos);
@property (class, nonatomic, readonly) UIColor *systemGroupedBackgroundColor            API_AVAILABLE(ios(13.0)) API_UNAVAILABLE(tvos, watchos);
@property (class, nonatomic, readonly) UIColor *secondarySystemGroupedBackgroundColor   API_AVAILABLE(ios(13.0)) API_UNAVAILABLE(tvos, watchos);
@property (class, nonatomic, readonly) UIColor *tertiarySystemGroupedBackgroundColor    API_AVAILABLE(ios(13.0)) API_UNAVAILABLE(tvos, watchos);

#pragma mark Fill colors
@property (class, nonatomic, readonly) UIColor *systemFillColor                         API_AVAILABLE(ios(13.0)) API_UNAVAILABLE(tvos, watchos);
@property (class, nonatomic, readonly) UIColor *secondarySystemFillColor                API_AVAILABLE(ios(13.0)) API_UNAVAILABLE(tvos, watchos);
@property (class, nonatomic, readonly) UIColor *tertiarySystemFillColor                 API_AVAILABLE(ios(13.0)) API_UNAVAILABLE(tvos, watchos);
@property (class, nonatomic, readonly) UIColor *quaternarySystemFillColor               API_AVAILABLE(ios(13.0)) API_UNAVAILABLE(tvos, watchos);

#pragma mark Other colors
// 这两个是非动态颜色值
@property(class, nonatomic, readonly) UIColor *lightTextColor API_UNAVAILABLE(tvos);    // for a dark background
@property(class, nonatomic, readonly) UIColor *darkTextColor API_UNAVAILABLE(tvos);     // for a light background

@property(class, nonatomic, readonly) UIColor *groupTableViewBackgroundColor API_DEPRECATED_WITH_REPLACEMENT("systemGroupedBackgroundColor", ios(2.0, 13.0), tvos(13.0, 13.0));
@property(class, nonatomic, readonly) UIColor *viewFlipsideBackgroundColor API_DEPRECATED("", ios(2.0, 7.0)) API_UNAVAILABLE(tvos);
@property(class, nonatomic, readonly) UIColor *scrollViewTexturedBackgroundColor API_DEPRECATED("", ios(3.2, 7.0)) API_UNAVAILABLE(tvos);
@property(class, nonatomic, readonly) UIColor *underPageBackgroundColor API_DEPRECATED("", ios(5.0, 7.0)) API_UNAVAILABLE(tvos);

@end
```

- 上面系统提供的这些颜色种类，根本不能满足我们正常开发的需要，大部分的颜色值也都是自定义。
- 系统也为我们提供了创建自定义颜色的方法。

```
@available(iOS 13.0, *)
public init(dynamicProvider: @escaping (UITraitCollection) -> UIColor)
```

在 `Objective-C` 中

```
+ (UIColor *)colorWithDynamicProvider:(UIColor * (^)(UITraitCollection *traitCollection))dynamicProvider API_AVAILABLE(ios(13.0), tvos(13.0)) API_UNAVAILABLE(watchos);
- (UIColor *)initWithDynamicProvider:(UIColor * (^)(UITraitCollection *traitCollection))dynamicProvider API_AVAILABLE(ios(13.0), tvos(13.0)) API_UNAVAILABLE(watchos);
```

- 上面的方法接受一个闭包 (`block`) 。
- 当系统在 `LightMode` 和 `DarkMode` 之间相互切换时就会自动触发此回调。
- 回调返回一个 `UITraitCollection`，可根据改对象判断是那种模式。

```
fileprivate func getColor() -> UIColor {
    return UIColor { (collection) -> UIColor in
        if (collection.userInterfaceStyle == .dark) {
            return UIColor.red
        }
        return UIColor.green
    }
}
```

除了上述两个方法之外，`UIColor` 还增加了一个实例方法。

```
// 通过当前 traitCollection 得到对应 UIColor
@available(iOS 13.0, *)
open func resolvedColor(with traitCollection: UITraitCollection) -> UIColor
```

**CGColor**

- `UIColor` 只是设置背景色和文字颜色的类，可以动态的设置。
- 可是如果是需要设置类似边框颜色等属性时，又该如何处理呢？
- 设置上述边框属性，需要用到 `CGColor` 类，但是在 `iOS13` 中 `CGColor` 并不是动态颜色值，只能表示一种颜色。
- 在监听模式改变的方法中`traitCollectionDidChange`，根据不同的模式进行处理。

```
override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {

    super.traitCollectionDidChange(previousTraitCollection)
    
    // 每次模式改变的时候, 这里都会执行
    if (previousTraitCollection?.userInterfaceStyle == .dark) {
        redView.layer.borderColor = UIColor.red.cgColor
    } else {
        redView.layer.borderColor = UIColor.green.cgColor
    }
}
```

**图片适配**

- 在 `iOS` 中，图片基本都是放在 `Assets.xcassets` 里面，所以图片的适配，我们就相对麻烦一些了。
- 正常情况下都是下面这中处理方式。

![Appearances None](https://itenfay.github.io/images/ios13_dark/appearances_none.png)


- 需要适配不同模式的情况下，需要两套不同的图片，并做如下设置。
- 在设置 `Appearances` 时，我们选择 `Any, Dark` 就可以了 (只需要适配深色模式和非深色模式)。

![Appearances Dark ](https://itenfay.github.io/images/ios13_dark/appearances_dark.png)

### 适配相关

**当前页面模式**

- 原项目中如果没有适配 `Dark Mode`，当你切换到 `Dark Mode` 后，你可能会发现有些部分页面的颜色自动适配了。
- 未设置过背景颜色或者文字颜色的组件，在 `Dark Mode` 模式下，就是黑色的。
- 这里我们就需要真对该单独 `App` 强制设置成 `Light Mode` 模式。

```
// 设置改属性, 只会影响当前的视图, 不会影响前面的 controller 和后续 present 的 controller
@available(iOS 13.0, *)
open var overrideUserInterfaceStyle: UIUserInterfaceStyle

// 使用示例
override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {
    super.traitCollectionDidChange(previousTraitCollection)
    
    // 每次模式改变的时候, 这里都会执行
    if (previousTraitCollection?.userInterfaceStyle == .dark) {
        // 在Dark模式下, 强制改成Light模式
        overrideUserInterfaceStyle = .light
    }
}
```

**强制项目的显示模式**

- 上面这种方式只能针对某一个页面修改，如果需要对整个项目禁用 `Dark` 模式。
- 可以通过修改 `window` 的 `overrideUserInterfaceStyle` 属性。
- 在 `Xcode11` 创建的项目中，`window` 从 `AppDelegate` 移到 `SceneDelegate` 中，添加下面这段代码，就会做到全局修改显示模式。

```
let scene = UIApplication.shared.connectedScenes.first?.delegate as? SceneDelegate
scene?.window?.overrideUserInterfaceStyle = .light
```

在之前的项目中，可以在 `AppDelegate` 设置如下代码：

```
window.overrideUserInterfaceStyle = .light
```

我创建的项目，上述代码的确会强制改变当前的模式，但是状态栏的显示不会被修改。

**终极方案**

- 需要在 `info.plist` 文件中添加 `User Interface Style` 配置，并设置为 `Light`。

```
<key>UIUserInterfaceStyle</key>
<string>Light</string>
```

问题又来了，即使做了上面的修改，在 `React Native` 中，状态栏的依然是根据不同的模式显示不同的颜色，该如何处理？

**Status Bar 更新**

在 `iOS13` 中苹果对于 `Status Bar` 也做了部分修改，在 `iOS13` 之前

```
public enum UIStatusBarStyle : Int {
    case `default` // 默认文字黑色

    @available(iOS 7.0, *)
    case lightContent // 文字白色
}
```

从 `iOS13` 开始 `UIStatusBarStyle` 一共有三种状态

```
public enum UIStatusBarStyle : Int {
    case `default` // 自动选择黑色或白色

    @available(iOS 7.0, *)
    case lightContent // 文字白色
    
    @available(iOS 13.0, *)
    case darkContent // 文字黑色
}
```

在 `React Native` 的代码中，设置状态栏的颜色为黑色，代码如下：

```
<StatusBar barStyle={'dark-content'} />
```

- 上面这段代码在 `iOS13` 系统的手机中是无效的。
- 虽然上面的代码中设置了 `dark-content` 模式，但是在 `iOS` 原生代码中 `dark-content` 实际是 `UIStatusBarStyleDefault。
- 在文件 `RCTStatusBarManager.m` 中

```
RCT_ENUM_CONVERTER(UIStatusBarStyle, (@{
  @"default": @(UIStatusBarStyleDefault),
  @"light-content": @(UIStatusBarStyleLightContent),
  @"dark-content": @(UIStatusBarStyleDefault),
}), UIStatusBarStyleDefault, integerValue);
```

修改上面代码即可

```
@"dark-content": @(@available(iOS 13.0, *) ? UIStatusBarStyleDarkContent : UIStatusBarStyleDefault),
```

### iOS13 其他更新

**Apple 登录**

> Sign In with Apple will be available for beta testing this summer. It will be required as an option for users in apps that support third-party sign-in when it is commercially available later this year.

*  如果 `APP` 支持三方登陆 (`Facbook`、`Google`、微信、`QQ`、支付宝等)，就必须支持 `Apple` 登陆，且要放置前边。
* 至于 `Apple` 登录按钮的样式，建议支持使用 `Apple` 提供的按钮样式，已经适配各类设备，可参考 *[Sign In with Apple](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.apple.com%2Fdesign%2Fhuman-interface-guidelines%2Fsign-in-with-apple%2Foverview%2F)* 。

**LaunchImage**

即将被废弃的 `LaunchImage`。

- 从 `iOS 8` 的时候，苹果就引入了 `LaunchScreen`，我们可以设置 `LaunchScreen` 来作为启动页。
- 现在你还可以使用 `LaunchImage` 来设置启动图，但是随着苹果设备尺寸越来越多，适配显然相对麻烦一些。
- 使用 `LaunchScreen` 的话，情况会变的很简单，`LaunchScreen`是支持 `AutoLayout` 和 `SizeClass` 的，所以适配各种屏幕都不在话下。
- ⚠️从2020年4月开始，所有 `App` 将必须提供 `LaunchScreen`，而 `LaunchImage` 即将退出历史舞台。

**UIWebView**

> `'UIWebView' was deprecated in iOS 12.0: No longer supported; please adopt WKWebView.`

从 `iOS 13` 开始也不再支持 `UIWebView` 控件了，请尽快采用 `WKWebView`。

```
@available(iOS, introduced: 2.0, deprecated: 12.0, message: "No longer supported; please adopt WKWebView.")
open class UIWebView : UIView, NSCoding, UIScrollViewDelegate { }
```
