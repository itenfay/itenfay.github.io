---
layout: post
title: "手把手教会你用 Markdown 写作"
author: "dyf"
date: 2019-06-10
tag: tool
---

## 手把手教会你用 Markdown 写作

Markdown 是一种「电子邮件」风格的「标记语言」，我强烈推荐所有写作者学习和掌握该语言。为什么？可以参考：

- [「为什么作家应该用 Markdown 保存自己的文稿」](https://www.jianshu.com/p/qqGjLN)
- [「Markdown写作浅谈」](https://www.jianshu.com/p/PpDNMG)

在此，我总结 Markdown 的优点如下：

- 纯文本，所以兼容性极强，可以用所有文本编辑器打开。
- 让你专注于文字而不是排版。
- 格式转换方便，Markdown 的文本你可以轻松转换为 html、电子书等。
- Markdown 的标记语法有极好的可读性。

当然，我既然如此推崇 Markdown ，也必定会教会你使用 Markdown ，这也是本文的目的所在。不过，虽然 [Markdown 的语法](https://guides.github.com/features/mastering-markdown/)已经足够简单，但是现有的 Markdown 语法说明更多的是写给 web 从业者看的，对于很多写作者来说，学习起来效率很低，现在，我特为写作者量身定做本指南，从写作者的实际需求出发，介绍写作者真正实用的常用格式，深入浅出、图文并茂地让您迅速掌握 Markdown 语法。

## 标题

这是最为常用的格式，在平时常用的的文本编辑器中大多是这样实现的：输入文本、选中文本、设置标题格式。

而在 Markdown 中，你只需要在文本前面加上 # 即可，同理、你还可以增加二级标题、三级标题、四级标题、五级标题和六级标题，总共六级，只需要增加 # 即可，标题字号相应降低。例如：
```
# 一级标题 
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题 
```

注：# 和「一级标题」之间建议保留一个字符的空格，这是最标准的 Markdown 写法。

标题案例截图如下：

![一级标题至六级标题](https://upload-images.jianshu.io/upload_images/1776763-36fcac755f4610dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 列表

列表格式也很常用，在 Markdown 中，你只需要在文字前面加上 - 就可以了，例如：
```
- 文本1
- 文本2
- 文本3
```

如果你希望有序列表，也可以在文字前面加上 1. 2. 3. 就可以了，例如：
```
1. 文本1
2. 文本2
3. 文本3
```

注：-、1.和文本之间要保留一个字符的空格。

列表案例截图如下：

![无序和有序列表](https://upload-images.jianshu.io/upload_images/1776763-5df15b7ac5c74d6b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 插入链接和图片

在 Markdown 中，插入链接不需要其他按钮，你只需要使用` [显示文本](链接地址)` 这样的语法即可，例如：
```
[简书](http://www.jianshu.com)
```

在 Markdown 中，插入图片不需要其他按钮，你只需要使用` [](图片链接地址)] `这样的语法即可，例如：
```
![](http://ww4.sinaimg.cn/bmiddle/aa397b7fjw1dzplsgpdw5j.jpg)
```

注：插入图片的语法和链接的语法很像，只是前面多了一个 ！。

插入链接和图片的案例截图：

![插入链接和图片](https://upload-images.jianshu.io/upload_images/1776763-2e993510c27b8592.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 引用

在我们写作的时候经常需要引用他人的文字，这个时候引用这个格式就很有必要了，在 Markdown 中，你只需要在你希望引用的文字前面加上 > 就好了，例如：

```
> 一盏灯，一片昏黄；一简书，一杯淡茶。守着那一份淡定，品读属于自己的寂寞。
保持淡定，才能欣赏到最美丽的风景！保持淡定人生从此不再寂寞。
```
注：> 和文本之间要保留一个字符的空格。

最终显示的就是：
> 一盏灯，一片昏黄；一简书，一杯淡茶。守着那一份淡定，品读属于自己的寂寞。保持淡定，才能欣赏到最美丽的风景！保持淡定人生从此不再寂寞。

引用的案例截图：

![引用](https://upload-images.jianshu.io/upload_images/1776763-0583346794b4c121.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 粗体和斜体

Markdown 的粗体和斜体也非常简单，用两个 * 包含一段文本就是粗体的语法，用一个 * 包含一段文本就是斜体的语法。例如：
```
*一盏灯*，一片昏黄；**一简书**，一杯淡茶。守着那一份淡定，品读属于自己的寂寞。
保持淡定，才能欣赏到最美丽的风景！ 保持淡定，人生从此不再寂寞。
```

最终显示的就是下文，其中「一盏灯」是斜体，「一简书」是粗体：

*一盏灯*，一片昏黄；**一简书**，一杯淡茶。守着那一份淡定，品读属于自己的寂寞。保持淡定，才能欣赏到最美丽的风景！保持淡定，人生从此不再寂寞。

粗体和斜体的案例截图：

![粗体斜体](https://upload-images.jianshu.io/upload_images/1776763-384af05f47c20224.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 代码引用

需要引用代码时，如果引用的语句只有一段，不分行，可以用 ` 将语句包起来。
如果引用的语句为多行，可以将```置于这段代码的首行和末行。

代码引用的案例截图：

![代码引用](https://upload-images.jianshu.io/upload_images/1776763-0e950ec8eee981af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 表格

相关代码：
```
| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |
```

显示效果：
| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |

## 显示链接中带括号的图片

代码如下:
```
![][1]
[1]: http://latex.codecogs.com/gif.latex?\prod%20\(n_{i}\)+1
````

![Woaw!](https://upload-images.jianshu.io/upload_images/1776763-353d70a2fa35b46e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 结语

以上几种格式是比较常用的格式，所以我针对这些语法做了比较详细的说明。除这些之外，Markdown 还有其他语法，如想了解和学习更多，可以参考这篇[『Markdown 语法说明』](https://guides.github.com/features/mastering-markdown/)。

## 参考
- [献给写作者的 Markdown 新手指南](https://www.jianshu.com/p/q81RER)
