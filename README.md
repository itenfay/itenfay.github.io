## Teng Fei's Blog 


#### [我的博客在这里→](https://chenxing640.github.io)

![](https://github.com/chenxing640/chenxing640.github.io/raw/master/images/tengfei-blog.png)


## 各版本特性


##### New Feature

* `-apple-system`被添加到了字体规则里面了，这套字体格式能将 iOS9 默认的新字体 **San Francisco** 表现的非常漂亮。
* 解决了代码过长自动换行的 bug , 替换为横向滚动条。


## 支持

* 你可以自由的 fork 。如果你能将我的信息和 github 的地址放在你的页面底部做成链接，我将非常感谢你。
* 如果你喜欢我的这个博客模板，请在 `chenxing640.github.io` 这个 repository 点个赞（右上角 **star** 一下）。


## 说明文档

* 开始
	* [环境要求](#environment)
	* [开始](#get-started)
	* [写一篇博文](#write-posts)
* 各组成部分
	* [侧边栏](#sidebar)
	* [mini-about-me](#mini-about-me)
	* [标签云](#featured-tags)
	* [好友链接](#friends)
	* [HTML5演示文档布局](#keynote-layout)
* 评论与 Google/Baidu Analytics
	* [评论](#comment)
	* [网站分析](#analytics) 
* 高级部分
	* [自定义](#customization)
	* [标题底图](#header-image)
	* [搜索展示标题-头文件](#seo-title)


#### Environment

如果你安装了 jekyll ，那你只需要在命令行输入`jekyll serve`就能在本地浏览器预览主题。你还可以输入`jekyll serve --watch`，这样可以边修改边自动运行修改后的文件。

经 [@BrucZhaoR](https://github.com/BruceZhaoR) 的测试，好像两个命令都是可以的自动运行修改后的文件的，刷新后可以实时预览。官方文件是建议安装 bundler ，这样你在本地的效果就跟在 github 上面是一样的。详情请见这里：https://help.github.com/articles/using-jekyll-with-pages/#installing-jekyll


#### Get Started

你可以通用修改 `_config.yml`文件来轻松的开始搭建自己的博客:

```
# Site settings
title: Teng Fei's Blog                # 你的博客网站标题
SEOTitle: Teng Fei's Blog | 腾飞的博客  # 在后面会详细谈到
description: "Teng Fei's Blog"        # 随便说点，描述一下

# SNS settings      
github_username: chenxing640      # 你的github账号
weibo_username: u/2617525300      # 你的微博账号，底部链接会自动更新的。

# Build settings
# paginate: 10                    # 一页你准备放几篇文章
```

Jekyll 官方网站还有很多的参数可以调，比如设置文章的链接形式...网址在这里：[Jekyll - Official Site](http://jekyllrb.com/) 中文版的在这里：[Jekyll中文](http://jekyllcn.com/)。


#### write-posts

要发表的文章一般以 markdown 的格式放在这里`_posts/`，你只要看看这篇模板里的文章你就立刻明白该如何设置。

yaml 头文件长这样:

```
---
layout:     post
title:      "Hello 2015"
subtitle:   "Hello World, Hello Blog"
date:       2015-01-29 12:00:00
author:     "Teng Fei"
header-img: "images/post-bg-distance.jpg"
tags: Life
---

```

#### SideBar

长这样:
![](https://github.com/chenxing640/chenxing640.github.io/raw/master/images/blog-sidebar.png)

设置是在 `_config.yml`文件里面的`Sidebar settings`那块。
```
# Sidebar settings
sidebar: true  #添加侧边栏
sidebar-about-description: "简单的描述一下你自己"
sidebar-avatar: /images/avatar.jpg #你的大头贴，请使用绝对地址.
```

侧边栏是响应式布局的，当屏幕尺寸小于992px的时候，侧边栏就会移动到底部。具体请见bootstrap栅格系统 <http://v3.bootcss.com/css/>


#### Mini About Me

Mini-About-Me 这个模块将在你的头像下面，展示你所有的社交账号。这个也是响应式布局，当屏幕变小时候，会将其移动到页面底部，只不过会稍微有点小变化，具体请看代码。


#### Featured Tags

看到这个网站 [Medium](http://medium.com) 的标签云非常的炫酷，所有我在将他加了进来。
这个模块现在是独立的，可以呈现在所有页面，包括主页和发表的每一篇文章标题的头上。

```
# Featured Tags
featured-tags: true
featured-condition-size: 1     # A tag will be featured if the size of it is more than this condition value
```

唯一需要注意的是`featured-condition-size`: A tag will be featured if the size of it is more than this condition value. 
 
内部有一个条件模板 `{% if tag[1].size > {{site.featured-condition-size}} %}` 是用来做筛选过滤的.


#### Friends

好友链接部分。这会在全部页面显示。

设置是在 `_config.yml`文件里面的`Friends`那块，自己加吧。

```
# Friends
friends: [
    {
        title: "Foo Blog",
        href: "http://foo.github.io/"
    },
    {
        title: "Bar Blog",
        href: "http://bar.github.io"
    }
]
```


#### Keynote Layout

HTML5 幻灯片的排版：

![](https://github.com/chenxing640/chenxing640.github.io/raw/master/images/blog-keynote.png)

这部分是用于占用html格式的幻灯片的，一般用到的是 Reveal.js, Impress.js, Slides, Prezi 等等.我认为一个现代化的博客怎么能少了放 html 幻灯的功能呢~

其主要原理是添加一个 `iframe`，在里面加入外部链接。你可以直接写到头文件里面去，详情请见下面的 yaml 头文件的写法。

```
---
layout: keynote
iframe: "http://chenxing640.com/js-module-7day/"
---
```

iframe 在不同的设备中，将会自动的调整大小。保留内边距是为了让手机用户可以向下滑动，以及添加更多的内容。


#### Comment

博客不仅支持多说 [duoshuo](http://duoshuo.com) 评论系统，也支持 [disqus](http://disqus.com) 评论系统。

disqus 国际比较流行，界面也很大气、简介，如果有人评论，还能实时通知，直接回复通知的邮件就行了。缺点是评论必须要去注册一个 disqus 账号，分享一般只有 Facebook 和 Twitter ，另外在墙内加载速度略慢了一点。想要知道长啥样，可以看以前的版本点 [这里](http://brucezhaor.github.io/about.html) 最下面就可以看到。

多说国内主流社交软件都有分享按钮，登陆方便，比较好管理，就是界面丑了一点。当然你是可以自定义界面的 css 的，详情请看多说开发者文档。

**首先**，你需要去注册一个账号，不管是 disqus 还是多说的。**不要直接使用我的啊！**

**其次**，你只需要在下面的 yaml 头文件中设置一下就可以了。

```
duoshuo_username: _你的用户名_
# 或者
disqus_username: _你的用户名_
```

**最后**多说是支持分享的，如果你不想分享，请这样设置：`duoshuo_share: false`。你可以同时使用两个评论系统，不过个人感觉怪怪的。


#### Analytics

网站分析，现在支持百度统计和 Google Analytics 。需要去官方网站注册一下，然后将返回的 code 贴在下面：

```
# Baidu Analytics
ba_track_id: 4cc1f2d8f3067386cc5cdb626a202900

# Google Analytics
ga_track_id: 'UA-49627206-1'  # Format: UA-xxxxxx-xx
ga_domain: chenxing640.com
```


#### Customization

如果你喜欢折腾，你可以去自定义我的这个模板的 code ，[Grunt](gruntjs.com) 的环境已经搭好了。（非常感谢 Clean Blog 这个模板）

There are a number of tasks it performs like minification of the JavaScript, compiling of the LESS files, adding banners to keep the Apache 2.0 license intact, and watching for changes. Run the grunt default task by entering `grunt ` into your command line which will build the files. You can use `grunt watch` if you are working on the JavaScript or the LESS.

**Try to understand code in `_include/` and `_layouts/`, then you can modify Jekyll [Liquid](https://github.com/Shopify/liquid/wiki) template directly to do more creative customization.**


#### Header Image

标题底图是可以自己选的，看看几篇示例post你就知道如何设置了，详情请见：[【_posts】](https://github.com/chenxing640/chenxing640.github.io/tree/master/_posts)。
  
标题底图的选取完全是看个人的审美了，我也帮不了你。每一篇文章可以有不同的底图，你想放什么就放什么，最后宽度要够，大小不要太大，否则加载慢啊。

但是需要注意的是本模板的标题是**白色**的，所以背景色要设置为**灰色**或者**黑色**，总之深色系就对了。


#### SEO Title

我的博客标题是 **“Teng Fei's Blog”**，但是我想要在搜索的时候显示 **“Teng Fei's Blog | 腾飞的博客”** ，这个就需要 **SEO Title** 来定义了。

其实这个 **SEO Title** 就是定义了<head><title>标题</title></head>，这个里面的东西和多说分享的标题，你可以自行修改的。


## 致谢

- 感谢 [Huxpro/huxpro.github.io](https://github.com/Huxpro/huxpro.github.io) 这个作者。 
- 感谢 [IronSummitMedia/startbootstrap-clean-blog-jekyll](https://github.com/IronSummitMedia/startbootstrap-clean-blog-jekyll) 这个作者。 
- 感谢 [@BrucZhaoR](https://github.com/BruceZhaoR) 的中文翻译。 
- 感谢 Jekyll、Github Pages 和 Bootstrap!


## 模板

如果你需要构建自己的 GitHub 技术博客，你就从[这里](https://github.com/chenxing640/chenxing640.github.io.git)克隆模板。


## 欢迎反馈

如果你注意到任何问题，被卡住或只是想聊天，请随意创建一个问题。我很乐意帮助你。
