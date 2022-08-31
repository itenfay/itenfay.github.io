---
layout: post
title: "谈谈 iOS 中对图像的模糊处理"
author: "chenxing"
date: 2019-08-08
tag: iOS
---


在 iOS 开发中，我们经常会遇到给图像添加蒙版、模糊效果或者毛玻璃处理，为了提高开发效率和方便各位小伙伴使用，我专门写一个 `DYFBlurEffect` 类。

我们使用 `DYFBlurEffect` 可以快速实现模糊图像，并支持系统 UIVisualEffectView。

接下来一起阅读它的使用说明：

## 使用说明

**1. 实例化**

```
// Lazy load
- (DYFBlurEffect *)blurEffect {
    if (!_blurEffect) {
        _blurEffect = [[DYFBlurEffect alloc] init];
    }
    return _blurEffect;
}
```

**2. 使用 CoreGraphics and vImage**
    
```
// Uses a `DYFBlurEffectStyle` style.
self.imgView.image = [self.blurEffect blurryImage:image style:DYFBlurEffectLight];

// Tints with a color.
self.imgView.image = [self.blurEffect blurryImage:image tintColor:[UIColor colorWithRed:40/255.0 green:40/255.0 blue:40/255.0 alpha:1]];
```

```
/**
Blur out an image with an original image, a blur radius, tint with a color, a saturation delta factor and a mask image.
*/
- (UIImage *)blurryImage:(UIImage *)image blurRadius:(CGFloat)blurRadius tintColor:(UIColor *)tintColor saturationDeltaFactor:(CGFloat)saturationDeltaFactor maskImage:(UIImage *)maskImage;
```

**3. 使用 UIVisualEffectView (Available iOS 8.0 or later)**

```
UIVisualEffectView *blurView = [self.blurEffect blurViewWithStyle:UIBlurEffectStyleLight];
blurView.frame = self.imgView.bounds;
//blurView.tag = 10;
//blurView.userInteractionEnabled = YES;
[self.view addSubview:blurView];
```

**4. 使用 CoreImage**

```
 self.imgView.image = [self.blurEffect coreImage:image blurRadius:10];
```

## 最后

附上 `DYFBlurEffect` 项目地址：[https://github.com/chenxing640/DYFBlurEffect](https://github.com/chenxing640/DYFBlurEffect)，觉得还行呢，麻烦顺手给个star。对开发实现图像的模糊处理功能的小伙伴，希望能够解决你的一些问题。即使不能，也希望能给你提供一些思路。

支持 Pods，在 Podfile 文件中添加：pod 'DYFBlurEffect'。
