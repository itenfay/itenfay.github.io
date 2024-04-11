---
layout: post
title: "安装 CocoaPods 及使用详解"
author: "Teng Fei"
date: 2017-11-08
tag: tool
---


> cocoapods 官网：[https://guides.cocoapods.org](https://guides.cocoapods.org)


## 一、什么是 CocoaPods

每种语言发展到一个阶段，就会出现相应的依赖管理工具，例如 Java 语言的 Maven，nodejs 的 npm。随着 iOS 开发者的增多，业界也出现了为 iOS 程序提供依赖管理的工具，它的名字叫做：CocoaPods。CocoaPods 项目的源码在 Github 上管理。该项目开始于 2011 年 8 月 12 日，经过多年发展，现在已经成为 iOS 开发的依赖管理标准工具。开发 iOS 项目不可避免地要使用第三方开源库，CocoaPods 的出现使得我们可以节省设置和更新第三方开源库的时间。


## 二、为什么要使用 CocoaPods

在使用 CocoaPods 之前，开发项目需要用到第三方开源库的时候，我们需要把开源库的源代码复制到项目中，添加一些依赖框架和动态库，设置 -ObjC，-fno-objc-arc 等参数，管理他们的更新在使用 CocoaPods 后，我们只需要把用到的开源库放到一个名为 Podfile 的文件中，然后执行 pod install/update 就可以了，Cocoapods 就会自动将这些第三方开源库的源码下载或更新下来，并且为我们的工程设置好相应的系统依赖和编译参数。


## 三、CocoaPods 的原理

CocoaPods 的原理是将所有的依赖库都放到另一个名为 Pods 的项目中，然后让主项目依赖 Pods 项目，这样，源码管理工作都从主项目移到了 Pods 项目中。Pods 项目最终会编译成一个名为 libPods.a 的文件，主项目只需要依赖这个 .a 文件即可。


## 四、CocoaPods 的安装

CocoaPods 可以方便地通过Mac自带的 RubyGems 安装。

打开 Terminal（ Mac 电脑自带的终端），

![Terminal](https://chenxing640.github.io/images/terminal.png)

然后按照以下提示操作：

### 1. 查看当前 ruby 版本

```
ruby -v
```

显示如下:

```
ruby 2.5.3p105 (2018-10-18 revision 65156) [x86_64-darwin18]
```

### 2. 升级 ruby 环境，首先安装 rvm

执行以下命令：

```
curl -L get.rvm.io | bash -s stable

source ~/.bashrc
source ~/.bash_profile
```

### 3. 查看 rvm 版本

```
rvm -v 
```

显示如下:

```
rvm 1.29.7 (latest) by Michal Papis, Piotr Kuczynski, Wayne E. Seguin [https://rvm.io]
```

### 4. 列出 ruby 可安装的版本信息

```
rvm list known
```

显示如下:

```
# MRI Rubies
[ruby-]1.8.6[-p420]
[ruby-]1.8.7[-head] # security released on head
[ruby-]1.9.1[-p431]
[ruby-]1.9.2[-p330]
[ruby-]1.9.3[-p551]
[ruby-]2.0.0[-p648]
[ruby-]2.1[.10]
[ruby-]2.2[.10]
[ruby-]2.3[.8]
[ruby-]2.4[.5]
[ruby-]2.5[.3]
[ruby-]2.6[.0] #(最新版本)
[ruby-]2.7[.0-preview3] #(预览版，不稳定，请慎用)
ruby-head
......
```

### 5. 安装一个 ruby 版本

这里我选择的是 2.6.0 版本，当然你也可以选择其他的。

```
rvm install 2.6.0
# 注意：安装过程中需要两次按下 Enter 键, 第二次按下后需要输入电脑访问密码 (不可见，只管输入就行) ；
# 如果你电脑没有安装 Xcode 和 Command Line Tools for Xcode 以及 Homebrew 会自动下载安装，建议提前安装这三者。
```

这里很多小伙伴会遇到错误，大部分是因为没有 [安装 Homebrew](https://www.jianshu.com/p/bca8fc1ff3f0) 造成。输入以下命令回车执行：

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
如遇到安装 Homebrew 报错：curl: (7) Failed to connect to raw.githubusercontent.com port 443: Connection refused，就请参考 [解决方案1](https://blog.csdn.net/WinstonLau/article/details/103855675) 或者 [解决方案2](https://www.jianshu.com/p/c2e829027b0a) 。

### 6. 设置默认版本

```
rvm use 2.6.0 --default
```

### 7. 设置 ruby 的软件源

这是因为 ruby 的软件源 rubygems.org 使用亚马逊的云服务，被我天朝屏蔽了，需要更新一下 ruby 的源，过程如下：

```
gem sources -l  # 查看当前 ruby 的源
gem sources --remove https://rubygems.org/  # 移除当前 ruby 的源
gem sources -a https://gems.ruby-china.com # 设置当前 ruby 的源为我天朝的
gem sources -l # 再次查看当前 ruby 的源
```

如果 Terminal 输出：

```
*** CURRENT SOURCES ***

https://gems.ruby-china.com
```

就证明 ruby 的软件源已经设置 OK 了。

### 8. 设置 gem 为最新版本

如果 gem 版本太旧，可以尝试用如下命令升级 gem：

在 Terminal 输入以下命令：

```
sudo gem update --system
```

升级成功后会提示: `Latest version currently installed. Aborting.`

### 9. 正式开始安装 CocoaPods

```
sudo gem install -n /usr/local/bin cocoapods
````

在最新版本 macOS 系统中不慎使用了`sudo gem install  cocoapods`，如果报以下错误：

```
ERROR:  While executing gem ... (Errno::EPERM)

Operation not permitted - /usr/bin/xcodeproj
```

就请使用 `sudo gem install -n /usr/local/bin cocoapods`。

### 10. 如果安装了多个 Xcode，就选择使用下面的命令

一般选择最近的 Xcode 版本。

```
sudo xcode-select -switch /Applications/Xcode.app/Contents/Developer
````

### 11. 安装本地库

```
pod setup
```

执行以上命令后，终端显示如下：

```
Setting up CocoaPods master repo
  $ /usr/bin/git clone https://github.com/CocoaPods/Specs.git master --progress
  Cloning into 'master'...
  remote: Counting objects: 1879515, done.        
  remote: Compressing objects: 100% (321/321), done.        
  Receiving objects:  21% (404525/1879515), 73.70 MiB | 22.00 KiB/

```

然后就是漫长的等待，当然，网络好的情况下会更快。

要查看文件下载进度，可以另外再打开一个终端窗口 (快捷键：选中终端按下 Command+N 组合键)，输入以下命令回车执行

```
cd ~/.cocoapods
du -sh *
```

执行 du -sh * 之后会显示已下载的文件大小，可以多次执行来监看下载进度，如果之前还有文件大小，后来变成 0 了，可能是网络问题，下载已经中断了，需要结束命令并重新执行 pod setup。

安装成功后，你会看到: `Setup completed`

### 12. 查找第三方库，检查 CocoaPods 是否可用

输入以下命令回车执行，第一次使用可能要等一会。

```
 pod search DYFCodeScanner
```

这时有可能出现 `[!] Unable to find a pod with name, author, summary, or description matching DYFCodeScanner`。

这是因为之前 pod search 的时候生成了缓存文件 `search_index.json`， 执行 `rm ~/Library/Caches/CocoaPods/search_index.json` 来删除此文件，然后再次输入 pod search DYFCodeScanner 进行搜索，这时会提示 Creating search index for spec repo 'master'..，等待一会将会出现搜索结果如下：

![Terminal](https://chenxing640.github.io/images/pod_search_result.png)

## 五、Cocoapods 的具体使用

新建一个 Xcode 工程，使用终端 cd 到工程目录下，创建 Podfile 文件：

```
pod init
```

之后就可以在项目目录里看到一个 Podfile 文件，打开 Podfile 文件：

```
open -e Podfile
```

添加或者修改：

```
platform :ios, '8.0' # 注明你的开发平台以及版本，'8.0'忽略不写即为最新版本

target 'TestMj' do
    # use_frameworks!
    pod 'DYFCodeScanner', '~> 1.0.1' # '~> 1.0.1'为版本号，忽略不写即为最新版本
end
```

一个 QPlayer 项目的 Podfile 文件的完整格式，例如：

```
platform :ios, '8.0'

target 'QPlayer' do
    # use_frameworks!
    
    # Pods for QPlayer
    pod 'MJRefresh', '~> 3.1.12'
    pod 'SVBlurView', '~> 0.0.1'
    pod 'FDFullscreenPopGesture', '~> 1.1'
    pod 'PYSearch', '~> 0.8.8'
    pod 'MBProgressHUD+JDragon', '~> 0.0.3'
    pod 'ZFPlayer', '~> 3.1.5'
    pod 'ZFPlayer/ControlView'
    pod 'ZFPlayer/AVPlayer'
    # pod 'ZFPlayer/ijkplayer'
    pod 'ZFPlayer/KSYMediaPlayer'

end
```


保存后退出，开始下载：

```
pod install 
```

或者

```
pod install --no-repo-update --verbose
```


这样，DYFCodeScanner 就已经下载完成并且设置好了编译参数和依赖，以后使用的时候切记如下两点：

- 从此以后需要使用 Cocoapods 生成的 .xcworkspace 文件来打开工程，而不是使用以前的 .xcodeproj 文件。

- 每次更改了 Podfile 文件，都需要重新执行一次`pod update` 或者 `pod update --no-repo-update --verbose` 命令。

### 查看 CocoaPods 版本

```
pod --version
```

### 关于 Podfile.lock

当你执行 pod install 之后，除了 Podfile 外，CocoaPods 还会生成一个名为 Podfile.lock 的文件，Podfile.lock 应该加入到版本控制里面，不应该把这个文件加入到 .gitignore 中。因为 Podfile.lock 会锁定当前各依赖库的版本，之后如果多次执行 pod install 不会更改版本，要 pod update 才会改 Podfile.lock 了。这样多人协作的时候，可以防止第三方库升级时造成大家各自的第三方库版本不一致。

### 如何使用 CocoaPods 的镜像索引？

所有项目的 Podspec 文件都托管在 [https://github.com/CocoaPods/Specs](https://github.com/CocoaPods/Specs)，第一次执行 pod setup 时，CocoaPods 会将这些 podspec 索引文件更新到本地的 ~/.cocoapods 目录下，这个索引文件比较大，所以第一次更新时非常慢，友好人士在国内的服务器建立了 Cocoapods 索引库的镜像，所以执行索引跟新操作时候会快很多，具体操作方法如下：

```
pod repo remove master
pod repo add master https://gitee.com/akuandev/specs.git
pod repo update
```

这是使用 oschina 上的镜像，将以上代码中的 [https://gitee.com/akuandev/specs.git](https://gitee.com/akuandev/specs.git)  替换成 [https://gitcafe.com/akuandev/specs.git](https://gitcafe.com/akuandev/specs.git)，即可使用 gitcafe 上的镜像。

