---
layout: post
title: "详解 Apple Keychain 和 App 之间数据共享"
header-img: "images/keychain/keychain_services.png"
author: "dgynfi"
date: 2018-03-10
tag: iOS
---


## Keychain 介绍

Keychain Services 是 macOS 和 iOS 都提供一种安全地存储敏感信息的工具，比如：”网络密码：用于保存访问服务器或者网站，通用密码：用来保存应用程序或者数据库密码“。与此同时，用于认证的证书、密钥和身份信息，也可以存储在 Keychain 中。Keychain Services 的安全机制保证了存储这些敏感信息不会被窃取。简单说来，Keychain 就是一个安全容器。另附 [Keychain Services | Apple Developer Documentation](https://developer.apple.com/documentation/security/keychain_services)。

*PS: 在 iOS 中 Keychian 依赖用于签名的 provisioning profile 描述文件，确保发布不同版本的时候，使用同一个 provisioning profile 文件。*

![Keychain Access FTP Server Flow](https://dgynfi.github.io/images/keychain/keychain_access_ftp_server_flow.png)

## Keychain 的结构

Keychain 可以包含任意数量的 Keychain Item。每一个 Keychain Item 包含数据和一组属性。对于一个需要保护的 Keychain Item，比如密码或者私钥（用于加密或者解密的 String 字节）数据是加密的，会被 Keychain 保护起来的；对于无需保护的 Keychain Item，例如: ”证书，数据未被加密“。

跟 Keychain Item 有关系的取决于 Item 的类型；应用程序中最常用的是网络密码（Internet passwrods）和普通的密码。正如你所想的，网络密码像安全域（security domain）、协议和路径等一些属性。在 macOS 中，当 Keychain 被锁的时候，加密的 Item 没办法访问，如果你想要该问被锁的 Item，就会弹出一个对话框，需要你输入对应 Keychain 的密码。当然，未有密码的 Keychain 你可以随时访问。但在 iOS 中，你只可以访问你自已的 Keychain Items。

## Keychain 的特点

- 数据并不存放在 App 的 Sanbox 中，即使删除了 App，资料依然保存在 Keychain 中。如果重新安装了 App，还可以从 Keychain 获取数据。

- Keychain 的数据可以用过 Group 方式，让数据可以在 App 间共享。不过得要相同 `TeamID`。

- Keychain 的数据是经过加密的。

## Keychain 的使用 

大多数应用需要用到 Keychain， 都用来添加一个密码，修改一个已存在 Keychain Item 或者取回密码。

Keychain 提供了以下的操作:

- SecItemAdd 添加一个 Item
- SecItemUpdate 更新已存在的 Item
- SecItemCopyMatching 搜索一个已存在的 Item
- SecItemDelete 删除一个 Item

推荐 Swift 开源库 [DYFSwiftKeychain](https://github.com/dgynfi/DYFSwiftKeychain)，另外，附上 (Objective-C) 版 [DYFKeychain](https://github.com/dgynfi/DYFKeychain)。

### 写入数据

![Putting data into a keychain](https://dgynfi.github.io/images/keychain/putting_data_into_keychain.png)

- 写入字符串

```
let keychain = DYFSwiftKeychain()
keychain.set("User Account Passcode", forKey: "kUserAccPasscode")
```

- 写入字节序列

```
let keychain = DYFSwiftKeychain()
keychain.set(data, forKey: "kCommSecureCode")
```

- 写入布尔值

```
let keychain = DYFSwiftKeychain()
keychain.set(true, forKey: "kFirstInstalledAndLaunched")
```

### 读取数据

- 读取字符串

```
let keychain = DYFSwiftKeychain()
let passcode = keychain.get("kUserAccPasscode")
```

- 读取字节序列

```
let keychain = DYFSwiftKeychain()
let data = keychain.getData("kCommSecureCode")
```

- 读取布尔值

```
let keychain = DYFSwiftKeychain()
keychain.getBool("kFirstInstalledAndLaunched")
```

### 删除数据

```
let keychain = DYFSwiftKeychain()
let _ = keychain.delete("kFirstInstalledAndLaunched") // Remove single key.
let _ = keychain.clear() // Delete everything from app's Keychain. Does not work on macOS.
```

### 高级选项

**1. 钥匙串Item访问选项**

```
let keychain = DYFSwiftKeychain()
keychain.set("xxx", forKey:"Key1", withAccess: .accessibleWhenUnlocked)
```

查看所有可用[访问选项](https://github.com/dgynfi/DYFSwiftKeychain/blob/master/SwiftKeychain/DYFSwiftKeychain.swift)的列表。

**2. 与其他设备同步钥匙串Item**

请注意，您不需要在应用程序的目标中启用 iCloud 或 Keychain 共享功能，使此功能工作。

```
// The first device
let keychain = DYFSwiftKeychain()
keychain.synchronizable = true
keychain.set("See you tomorrow!", forKey: "key12")

// The second device
let keychain = DYFSwiftKeychain()
keychain.synchronizable = true
let s = keychain.get("key12") // Returns "See you tomorrow!"
```

我们无法在 macOS 上进行钥匙串同步工作。

**3. 与其他应用程序共享钥匙串Item**

使用`accessGroup`属性访问共享钥匙串Item。在下面的示例中，我们指定一个访问组`9ZU3R2F3D4.com.omg.myapp.KeychainGroup`，它将用于设置、获取和删除`key1`项。

```
let keychain = DYFSwiftKeychain()
keychain.accessGroup = "9ZU3R2F3D4.com.omg.myapp.KeychainGroup" // Use your own access group.

keychain.set("hello world", forKey: "key1")
keychain.get("key1")
keychain.delete("key1")
keychain.clear()
```

*注*：watchOS 2.0与其配对设备之间无法共享钥匙串item：[https://forums.developer.apple.com/thread/5938](https://forums.developer.apple.com/thread/5938)

**3. 数据引用返回**

使用`asReference:true`参数将数据作为引用返回，这是 [NEVPNProtocol](https://developer.apple.com/documentation/networkextension/nevpnprotocol) 协议所需的。

```
let keychain = DYFSwiftKeychain()
keychain.set(data, forKey: "key1")
keychain.getData("key1", asReference: true)
```

## 共享 Keychain 数据

关于共享 Keychain 数据，你可以通过 Group 的方式让资料可以在 App 间共享，Google 系列的 App (Gmail、Google+、日历……) 就是通过这样的方式来记录使用者登入信息，只要使用者在其中一个 App 中完成登入了，其他的 App 也可以读取到相同的数据进行登入。

进入 Capabilities，打开 Keychain Sharing 功能

![Keychain Sharing](https://dgynfi.github.io/images/keychain/keychain_sharing.png)

开启 `Keychain Sharing` 后，会自动新增一个 Keychain Group，使用的是 `Bundle Identifier`，同时也会自动新增一个 entitlements 文件，里面也会有一个 Access Group，名为 `$(AppIdentifierPrefix)+Your Bundle Identifier`。

![Keychain Group Entitlements](https://dgynfi.github.io/images/keychain/keychain_group_entitlements.png)

`AppIdentifierPrefix` 是开发者的账号需要登录才会有，也就是开发者证书后小括号内的英文数字组合。使用 `$(AppIdentifierPrefix)` 只能被同一个开发者账号的 App 来存取，以防被有心人盗取。

![Xcode Signing](https://dgynfi.github.io/images/keychain/xcode_signing.png)

若其他的 App 也要存取当前 Keychain 的数据，就必需在 `Keychain Sharing` 开启后，新增相同的 Keychain Group (Access Group 会根据 Keychain Group 自动新增)。

*PS: 需要 `Info.plist` 新增一个键值对 Key: AppIdentifierPrefix，Value: $(AppIdentifierPrefix)，方便取得 `Identifier Prefix`。*

总结：写的有不好的地方希望大家指出，我会更正，大家有什么看不明白的，也可以在评论里面提问，我会尽力解答。另附 Apple 官方 [GenericKeychain](https://developer.apple.com/library/archive/samplecode/GenericKeychain/Introduction/Intro.html#//apple_ref/doc/uid/DTS40007797)。

---

最后，想了解更多详情，请查看我的 Demo，记得给个 Star，😝😝

Demo ( Objective-C )：[戳这里](https://github.com/dgynfi/DYFSwiftKeychain) <br >
Demo ( Swift )：[戳这里](https://github.com/dgynfi/DYFKeychain)
