---
layout: post
title: "如何轻松搞定 iOS 内购配置"
header-img: "images/iap/asc_bg.png"
author: "Teng Fei"
date: 2016-10-12
tag: iOS
---

## 配置流程

一个开发者账号首次配置 **内购(In-App Purchase)** 的新项目，必须先签署 Paid Applications Agreement（《付费应用程序协议》）

- 一、填写协议、税务和银行业务

1、登录 [https://appstoreconnect.apple.com](https://appstoreconnect.apple.com)，进入App Store Connect。

2、进入“协议、税务和银行业务”

![选择进入协议、税务和银行业务
](https://chenxing640.github.io/images/iap/atb_item.png)

3、付费应用程序，先签署《付费应用程序协议》，同意后状态变更为“用户信息待处理”，等待审核。

![签署协议后的状态](https://chenxing640.github.io/images/iap/agre_tax_bank.png)

4、状态更改完毕后，点击“开始设置税务、银行业务和联系信息”

I) 添加银行账户，按照要求填写相关内容。

![添加银行账户](https://chenxing640.github.io/images/iap/add_bank_account.png)

II) 选择报税表，并填写。

我是可爱的中国公民，在美国有没有商业活动，所以我选择填否。

![报税表-1](https://chenxing640.github.io/images/iap/tax_form.png)

![报税表-2](https://chenxing640.github.io/images/iap/u.s._commercial_actives.png)

然后继续填写报税表，按照要求填写（要是英文阅读有点困难，那就双击网页，应该会有翻译成中文的功能；如果没有的话，那就用词典或者借助翻译工具......，你懂得哈）， 不过没关系，都是一些基本信息。

![报税表-3](https://chenxing640.github.io/images/iap/tax_form2.png)

III) 填写联系信息

一共需要填写5个联系信息：高级管理、财务、技术、法务、营销（可重复，像我这种人才5个职位都是我，开玩笑的，勿当真）。

![填写联系信息-1](https://chenxing640.github.io/images/iap/tax_bank_contact.png)

![填写联系信息-2](https://chenxing640.github.io/images/iap/edit_contacts.png)

- 二、添加 App 内购买项目

1、上面的税务表填完了之后，点击“我的 App”，进入到项目 App 的信息页，点击功能，在弹出的页面点击 App 内购买项目后面的+。

![我的 App](https://chenxing640.github.io/images/iap/myapp_item.png)

![添加 App 内购买项目](https://chenxing640.github.io/images/iap/add_iap_items.png)

以消耗型为例

![消耗型项目](https://chenxing640.github.io/images/iap/iap_item_types.png)

2、创建完成之后，填写内购买项目信息

![内购买项目信息填写-1](https://chenxing640.github.io/images/iap/iap_item_config.png)

![内购买项目信息填写-2](https://chenxing640.github.io/images/iap/iap_item_localization.png)

![内购买项目信息填写-3](https://chenxing640.github.io/images/iap/iap_item_astg.png)

![内购买项目信息填写-4](https://chenxing640.github.io/images/iap/iap_item_mark.png)

3、填写信息完成了，点击右上角的 “存储”，然后点击左边 “App 内购买项目”。出现“元数据丢失”说明里面信息没填写完整，在点进去填写，直到显示“准备提交”。

![元数据丢失](https://chenxing640.github.io/images/iap/iap_item_warning.png)

- 三、添加沙箱测试员

完成添加内购买项目后，点击“用户和访问”，选择“沙箱技术学测试员”，在弹出的页面选择用户，点击+，添加沙箱测试员。

![添加沙箱测试员-1](https://chenxing640.github.io/images/iap/add_sandbox_tester.png)

![添加沙箱测试员-2](https://chenxing640.github.io/images/iap/sandbox_tester_profile.png)

- 四、打开 Xcode 应用内购买配置

我们需要在工程里配置好证书，测试证书是必须的，因为内购需要连接到苹果的App Store，需要正式的测试证书才能测试，同时打开工程中的这一配置，如下图所示。

![应用内购买配置](https://chenxing640.github.io/images/iap/turnon_iap_capabilities.png)

## 开发实现

推荐阅读[《iOS 内购完整的编程指南》](https://chenxing640.github.io/2016/10/16/in-app-purchase-complete-programming-guide-for-iOS/)，这里就不详细阐述了。
