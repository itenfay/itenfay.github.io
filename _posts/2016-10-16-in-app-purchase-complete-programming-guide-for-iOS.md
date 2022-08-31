---
layout: post
title: "iOS 内购完整的编程指南"
header-img: "images/iap/asc_bg.png"
author: "chenxing"
date: 2016-10-16
tag: iOS
---


## 什么是内购？

内购(In-App Purchase)，顾名思义就是在应用内购买。在了解完其含义后，我们还需知道 **内购(In-App Purchase)** 和 **苹果支付(Apple Pay)** 的区别。

*  苹果支付(Apple Pay)：是一种支付方式，跟支付宝、微信支付是类似的，这里就不详细介绍了。

*  内购(In-App Purchase)：只要在 iOS/iPadOS 设备上的 App 里购买非实物产品 (也就是虚拟产品，如：“qq 币、鱼翅、电子书......”) ，都需要走内购流程，苹果从这里面抽走 30% 分成。

## 内购编程指南

一般来说，开发者刚接触到**内购**，都会遇到流程不清楚、代码逻辑混乱和各种踩坑。那么，如何一次性搞定 iOS 内购？接下来本文将分成三个部分讲解：

*  前期准备工作

*  开发实现

*  注意事项和踩坑解决办法

## 一、前期准备工作

*  接入内购前，建议阅读一下苹果官方的文档
  
  *  [《In-App Purchase | Apple Developer Documentation》](https://developer.apple.com/documentation/storekit/in-app_purchase)
  
  *  [《App 内购买项目配置流程》](https://help.apple.com/app-store-connect/#/devb57be10e7) 

*  App Store Connect 后台配置内购项目

  推荐阅读[《如何轻松搞定 iOS 内购配置》](https://chenxing640.github.io/2016/10/12/how-to-easily-complete-in-app-purchase-configuration-for-iOS/)，这里就不详细介绍了。

*  Xcode 工程配置

![Xcode Project Configuration](https://chenxing640.github.io/images/iap/iap_project_config.png)

*  开发实现流程

![IAP Implementation Flow](https://chenxing640.github.io/images/iap/iap_impl_flow.jpg)

## 二、开发实现

*PS：每个开发者帐户可在该帐户的所有 App 中创建最多 10,000 个 App 内购买项目产品。App 内购买项目共有四种类型：消耗型、非消耗型、自动续期订阅和非续期订阅。*

推荐 Swift 开源库（[DYFStore](https://link.jianshu.com?t=https://github.com/chenxing640/DYFStore)），使用此开源库可直接省去很多繁琐复杂的实现，提高工作效率。另附 Objective-C 版（[DYFStoreKit](https://link.jianshu.com?t=https://github.com/chenxing640/DYFStoreKit)）。

### 接入 StoreKit 准备

*  首先在项目工程中加入 `StoreKit.framework`

*  在实现文件导入模块 `import StoreKit`

*  在实现类遵守协议 `SKProductsRequestDelegate`, `SKPaymentTransactionObserver `

### 初始化工作

*  是否允许将日志输出到控制台，在 Debug 模式下将 `enableLog` 设置 `true`，查看内购整个过程的日志，在 Release 模式下发布 App 时将 `enableLog` 设置 `false`。

*  添加交易的观察者，监听交易的变化。

*  实例化数据持久，存储交易的相关信息。

*  遵守协议 `DYFStoreAppStorePaymentDelegate`，处理从 App Store 购买产品的付款。

```
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {

    // Wether to allow the logs output to console.
    DYFStore.default.enableLog = true

    // Adds an observer that responds to updated transactions to the payment queue.
    // If an application quits when transactions are still being processed, those transactions are not lost. The next time the application launches, the payment queue will resume processing the transactions. Your application should always expect to be notified of completed transactions.
    // If more than one transaction observer is attached to the payment queue, no guarantees are made as to the order they will be called in. It is recommended that you use a single observer to process and finish the transaction.
    DYFStore.default.addPaymentTransactionObserver()

    // Sets the delegate processes the purchase which was initiated by user from the App Store.
    DYFStore.default.delegate = self

    DYFStore.default.keychainPersister = DYFStoreKeychainPersistence()

    return true
}
```

### 处理从 App Store 购买产品的付款 

只有在 iOS 11.0 或更高的版本，才能处理用户从 App Store 商店发起购买产品的请求。因为这个接口 (API) 是在 iOS 11.0 或更高的版本才生效的。

```
// Processes the purchase which was initiated by user from the App Store.
func didReceiveAppStorePurchaseRequest(_ queue: SKPaymentQueue, payment: SKPayment, forProduct product: SKProduct) {
    
    if !DYFStore.canMakePayments() {
        self.showTipsMessage("Your device is not able or allowed to make payments!")
        return
    }
    
    // Get account name from your own user system.
    let accountName = "Handsome Jon"
    
    // This algorithm is negotiated with server developer.
    let userIdentifier = DYF_SHA256_HashValue(accountName) ?? ""
    DYFStoreLog("userIdentifier: \(userIdentifier)")
    
    DYFStore.default.purchaseProduct(product.productIdentifier, userIdentifier: userIdentifier)
}
```

> Indicates whether the user is allowed to make payments.
An iPhone can be restricted from accessing the Apple App Store. For example, parents can restrict their children’s ability to purchase additional content. Your application should confirm that the user is allowed to authorize payments before adding a payment to the queue. Your application may also want to alter its behavior or appearance when the user is not allowed to authorize payments.

### 创建商品查询的请求

有两种策略可用于从应用程序商店获取有关产品的信息。

**策略1：** 在开始购买过程，首先必须清楚有哪些产品标识符。App 可以使用其中一个产品标识符来获取应用程序商店中可供销售的产品的信息，并直接提交付款请求。

```
@IBAction func fetchesProductAndSubmitsPayment(_ sender: Any) {
    self.showLoading("Loading...")
    
    let productId = "com.hncs.szj.coin42"
    
    DYFStore.default.requestProduct(withIdentifier: productId, success: { (products, invalidIdentifiers) in
        
        self.hideLoading()
        
        if products.count == 1 {
            
            let productId = products[0].productIdentifier
            self.addPayment(productId)
            
        } else {
            
            self.showTipsMessage("There is no this product for sale!")
        }
        
    }) { (error) in
        
        self.hideLoading()
        
        let value = error.userInfo[NSLocalizedDescriptionKey] as? String
        let msg = value ?? "\(error.localizedDescription)"
        self.sendNotice("An error occurs, \(error.code), " + msg)
    }
}

private func addPayment(_ productId: String) {
    
    if !DYFStore.canMakePayments() {
        self.showTipsMessage("Your device is not able or allowed to make payments!")
        return
    }
    
    // Get account name from your own user system.
    let accountName = "Handsome Jon"
    
    // This algorithm is negotiated with server developer.
    let userIdentifier = DYF_SHA256_HashValue(accountName) ?? ""
    DYFStoreLog("userIdentifier: \(userIdentifier)")
    
    DYFStore.default.purchaseProduct(productId, userIdentifier: userIdentifier)
}
```

**策略2：** 在开始购买过程，首先必须清楚有哪些产品标识符。App 从应用程序商店获取有关产品的信息，并向用户显示其商店用户界面。App 中销售的每个产品都有唯一的产品标识符。App 使用这些产品标识符获取有关应用程序商店中可供销售的产品的信息，例如定价，并在用户购买这些产品时提交付款请求。

![](https://chenxing640.github.io/images/iap/iap_flow.png)

```
func fetchProductIdentifiersFromServer() -> [String] {
    
    let productIds = [
        "com.hncs.szj.coin42",   // 42 gold coins for ￥6.
        "com.hncs.szj.coin210",  // 210 gold coins for ￥30.
        "com.hncs.szj.coin686",  // 686 gold coins for ￥98.
        "com.hncs.szj.coin1386", // 1386 gold coins for ￥198.
        "com.hncs.szj.coin2086", // 2086 gold coins for ￥298.
        "com.hncs.szj.coin4886", // 4886 gold coins for ￥698.
        "com.hncs.szj.vip1",     // non-renewable vip subscription for a month.
        "com.hncs.szj.vip2"      // Auto-renewable vip subscription for three months.
    ]
    
    return productIds
}

@IBAction func fetchesProductsFromAppStore(_ sender: Any) {
    self.showLoading("Loading...")
    
    let productIds = fetchProductIdentifiersFromServer()
    
    DYFStore.default.requestProduct(withIdentifiers: productIds, success: { (products, invalidIdentifiers) in
        
        self.hideLoading()
        
        if products.count > 0 {
            
            self.processData(products)
            
        } else if products.count == 0 &&
            invalidIdentifiers.count > 0 {
            
            // Please check the product information you set up.
            self.showTipsMessage("There are no products for sale!")
        }
        
    }) { (error) in
        
        self.hideLoading()
        
        let value = error.userInfo[NSLocalizedDescriptionKey] as? String
        let msg = value ?? "\(error.localizedDescription)"
        self.sendNotice("An error occurs, \(error.code), " + msg)
    }
}

private func processData(_ products: [SKProduct]) {
    
    var modelArray = [DYFStoreProduct]()
    
    for product in products {
        
        let p = DYFStoreProduct()
        p.identifier = product.productIdentifier
        p.name = product.localizedTitle
        p.price = product.price.stringValue
        p.localePrice = DYFStore.default.localizedPrice(ofProduct: product)
        p.localizedDescription = product.localizedDescription
        
        modelArray.append(p)
    }
    
    self.displayStoreUI(modelArray)
}

private func displayStoreUI(_ dataArray: [DYFStoreProduct]) {
    
    if !DYFStore.canMakePayments() {
        self.showTipsMessage("Your device is not able or allowed to make payments!")
        return
    }
    
    let storeVC = DYFStoreViewController()
    storeVC.dataArray = dataArray
    self.navigationController?.pushViewController(storeVC, animated: true)
}
```

### 创建购买产品的付款请求

1、不使用您的系统用户帐户 ID

```
DYFStore.default.purchaseProduct("com.hncs.szj.coin210")
```

2、使用您的系统用户帐户 ID

*  获取 SHA256 哈希值函数

```
public func DYF_SHA256_HashValue(_ s: String) -> String? {

    let digestLength = Int(CC_SHA256_DIGEST_LENGTH) // 32

    let cStr = s.cString(using: String.Encoding.utf8)!
    let cStrLen = Int(s.lengthOfBytes(using: String.Encoding.utf8))

    // Confirm that the length of C string is small enough
    // to be recast when calling the hash function.
    if cStrLen > UINT32_MAX {
        print("C string too long to hash: \(s)")
        return nil
    }

    let md = UnsafeMutablePointer<CUnsignedChar>.allocate(capacity: digestLength)

    CC_SHA256(cStr, CC_LONG(cStrLen), md)

    // Convert the array of bytes into a string showing its hex represention.
    let hash = NSMutableString()
    for i in 0..<digestLength {

        // Add a dash every four bytes, for readability.
        if i != 0 && i%4 == 0 {
            //hash.append("-")
        }
        hash.appendFormat("%02x", md[i])
    }

    md.deallocate()

    return hash as String
}
```

*  通过 SHA256 计算的用户帐户 ID

```
DYFStore.default.purchaseProduct("com.hncs.szj.coin210", userIdentifier: "A43512564ACBEF687924646CAFEFBDCAEDF4155125657")
```

### 恢复已购买的付款交易

在某些场景（如切换设备），App 需要提供恢复购买按钮，用来恢复之前购买的非消耗型的产品。

*  无绑定用户帐户 ID 的恢复

```
DYFStore.default.restoreTransactions()
```

*  绑定用户帐户 ID 的恢复

```
DYFStore.default.restoreTransactions(userIdentifier: "A43512564ACBEF687924646CAFEFBDCAEDF4155125657")
```

#### 创建刷新收据请求

如果 `Bundle.main.appStoreReceiptURL` 为 null，就需要创建刷新收据请求，获取付款交易的收据。

```
DYFStore.default.refreshReceipt(onSuccess: {
    self.storeReceipt()
}) { (error) in
    self.failToRefreshReceipt()
}
```

### 付款交易的变化通知

*  添加商店观察者，监听购买和下载通知 

```
func addStoreObserver() {
    NotificationCenter.default.addObserver(self, selector: #selector(DYFStoreManager.processPurchaseNotification(_:)), name: DYFStore.purchasedNotification, object: nil)
    NotificationCenter.default.addObserver(self, selector: #selector(DYFStoreManager.processDownloadNotification(_:)), name: DYFStore.downloadedNotification, object: nil)
}
```

*  在适当的时候，移除商店观察者

```
func removeStoreObserver() {
    NotificationCenter.default.removeObserver(self, name: DYFStore.purchasedNotification, object: nil)
    NotificationCenter.default.removeObserver(self, name: DYFStore.downloadedNotification, object: nil)
}
```

*  付款交易的通知处理

```
@objc private func processPurchaseNotification(_ notification: Notification) {

    self.hideLoading()

    self.purchaseInfo = (notification.object as! DYFStore.NotificationInfo)

    switch self.purchaseInfo.state! {
    case .purchasing:
        self.showLoading("Purchasing...")
        break
    case .cancelled:
        self.sendNotice("You cancel the purchase")
        break
    case .failed:
        self.sendNotice(String(format: "An error occurred, \(self.purchaseInfo.error!.code)"))
        break
    case .succeeded, .restored:
        self.completePayment()
        break
    case .restoreFailed:
        self.sendNotice(String(format: "An error occurred, \(self.purchaseInfo.error!.code)"))
        break
    case .deferred:
        DYFStoreLog("Deferred")
        break
    }

}
```

*  下载的通知处理

```
@objc private func processDownloadNotification(_ notification: Notification) {

    self.downloadInfo = (notification.object as! DYFStore.NotificationInfo)

    switch self.downloadInfo.downloadState! {
    case .started:
        DYFStoreLog("The download started")
        break
    case .inProgress:
        DYFStoreLog("The download progress: \(self.downloadInfo.downloadProgress)%%")
        break
    case .cancelled:
        DYFStoreLog("The download cancelled")
        reak
    case .failed:
        DYFStoreLog("The download failed")
        break
    case .succeeded:
        DYFStoreLog("The download succeeded: 100%%")
        break
    }
}
```

### 收据验证

*  验证 URL

```
/// The url for sandbox in the test environment.
private let sandboxUrl = "https://sandbox.itunes.apple.com/verifyReceipt"

/// The url for production in the production environment.
private let productUrl = "https://buy.itunes.apple.com/verifyReceipt"
```

*  常见的验证状态码和对应的描述

![](https://chenxing640.github.io/images/iap/iap_status_code.png)

```
/// Matches the message with the status code.
///
/// - Parameter status: The status code of the request response. More, please see [Receipt Validation Programming Guide](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html#//apple_ref/doc/uid/TP40010573-CH104-SW1)
/// - Returns: A tuple that contains status code and the description of status code.
public func matchMessage(withStatus status: Int) -> (Int, String) {
    var message: String = ""
    
    switch status {
    case 0:
        message = "The receipt as a whole is valid."
        break
    case 21000:
        message = "The App Store could not read the JSON object you provided."
        break
    case 21002:
        message = "The data in the receipt-data property was malformed or missing."
        break
    case 21003:
        message = "The receipt could not be authenticated."
        break
    case 21004:
        message = "The shared secret you provided does not match the shared secret on file for your account."
        break
    case 21005:
        message = "The receipt server is not currently available."
        break
    case 21006:
        message = "This receipt is valid but the subscription has expired. When this status code is returned to your server, the receipt data is also decoded and returned as part of the response. Only returned for iOS 6 style transaction receipts for auto-renewable subscriptions."
        break
    case 21007:
        message = "This receipt is from the test environment, but it was sent to the production environment for verification. Send it to the test environment instead."
        break
    case 21008:
        message = "This receipt is from the production environment, but it was sent to the test environment for verification. Send it to the production environment instead."
        break
    case 21010:
        message = "This receipt could not be authorized. Treat this the same as if a purchase was never made."
        break
    default: /* 21100-21199 */
        message = "Internal data access error."
        break
    }
    
    return (status, message)
}
```

*  客户端验证，不安全且容易被破解，不推荐使用

1、使用懒加载实例化 `DYFStoreReceiptVerifier`

```
lazy var receiptVerifier: DYFStoreReceiptVerifier = {
    let verifier = DYFStoreReceiptVerifier()
    verifier.delegate = self
    return verifier
}()
```

2、实现协议 `DYFStoreReceiptVerifierDelegate`

```
@objc func verifyReceiptDidFinish(_ verifier: DYFStoreReceiptVerifier, didReceiveData data: [String : Any])

@objc func verifyReceipt(_ verifier: DYFStoreReceiptVerifier, didFailWithError error: NSError)
```

3、验证收据

```
// Fetches the data of the bundle’s App Store receipt. 
let data = receiptData

self.receiptVerifier.verifyReceipt(data)

// Only used for receipts that contain auto-renewable subscriptions.
//self.receiptVerifier.verifyReceipt(data, sharedSecret: "A43512564ACBEF687924646CAFEFBDCAEDF4155125657")
```

*  服务器验证，相对安全，推荐

客户端通过接口将所需的参数上传至服务器，接口数据最好进行加密处理。然后服务器向苹果服务器验证收据并获取相应的信息，服务器比对产品 ID，Bundle Identifier，交易 ID，付款状态等信息后，若付款状态为0，通知客户端付款成功，客户端完成当前的交易。

推荐阅读 Apple 官方发布的收据验证编程指南（ [Receipt Validation Programming Guide](https://link.jianshu.com?t=https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html#//apple_ref/doc/uid/TP40010573-CH104-SW1) ）

### 完成交易

只有在客户端接收到付款成功并在收据校验通过后，才能完成交易。这样，我们可以避免刷单和破解应用内购买。如果我们无法完成校验收据，我们就希望 `StoreKit` 不断提醒我们还有未完成的付款。

```
DYFStore.default.finishTransaction(transaction)
```

### 交易信息存储

`DYFStore` 提供了两种数据存储方式 `DYFStoreKeychainPersistence` 和 `DYFStoreUserDefaultsPersistence`。

当客户端在付款过程中发生崩溃，导致 App 闪退，这时存储交易信息尤为重要。当 StoreKit 再次通知未完成的付款时，直接从 Keychain 中取出数据，进行收据验证，直至完成交易。

*  存储交易信息

```
func storeReceipt() {

    guard let url = DYFStore.receiptURL() else {
        self.refreshReceipt()
        return
    }
    
    do {
        let data = try Data(contentsOf: url)
        
        let info = self.purchaseInfo!
        let store = DYFStore.default
        let persister = store.keychainPersister!
        
        let transaction = DYFStoreTransaction()
        
        if info.state! == .succeeded {
            transaction.state = DYFStoreTransactionState.purchased.rawValue
        } else if info.state! == .restored {
            transaction.state = DYFStoreTransactionState.restored.rawValue
        }
        
        transaction.productIdentifier = info.productIdentifier
        transaction.userIdentifier = info.userIdentifier
        transaction.transactionTimestamp = info.transactionDate?.timestamp()
        transaction.transactionIdentifier = info.transactionIdentifier
        transaction.originalTransactionTimestamp = info.originalTransactionDate?.timestamp()
        transaction.originalTransactionIdentifier = info.originalTransactionIdentifier
        
        transaction.transactionReceipt = data.base64EncodedString()
        persister.storeTransaction(transaction)
        
        // Makes the backup data.
        let uPersister = DYFStoreUserDefaultsPersistence()
        if !uPersister.containsTransaction(info.transactionIdentifier!) {
            uPersister.storeTransaction(transaction)
        }
        
        self.verifyReceipt(data)
    } catch let error {
        
        DYFStoreLog("error: \(error.localizedDescription)")
        self.refreshReceipt()
        
        return
    }
}
```

*  移除交易信息

```
DispatchQueue.main.asyncAfter(delay: 1.5) {
    let info = self.purchaseInfo!
    let store = DYFStore.default
    let persister = store.keychainPersister!
    let identifier = info.transactionIdentifier!
    
    if info.state! == .restored {
        
        let transaction = store.extractRestoredTransaction(identifier)
        store.finishTransaction(transaction)
        
    } else {
        
        let transaction = store.extractPurchasedTransaction(identifier)
        // The transaction can be finished only after the client and server adopt secure communication and data encryption and the receipt verification is passed. In this way, we can avoid refreshing orders and cracking in-app purchase. If we were unable to complete the verification, we want `StoreKit` to keep reminding us that there are still outstanding transactions.
        store.finishTransaction(transaction)
    }
    
    persister.removeTransaction(identifier)
    if let id = info.originalTransactionIdentifier {
        persister.removeTransaction(id)
    }
}
```

## 注意事项和踩坑解决办法

*  产品配置好了，为何在测试购买时获取到产品信息无效呢？

  测试内购一定要用真机测试，产品信息如果无效，一般是产品还没有审核通过  ！

*  如果 App 没有实物购买，不移除支付宝、微信支付的 SDK 行吗？

  接入内购后，如果 App 没有实物购买，就必须把支付宝、微信支付的 SDK 删掉，如果 Apple 那边扫描出来，App 就会被拒审。

*  沙盒测试有时无响应

  1. 检查网络环境，尝试在 WiFi 或 蜂窝网络 切换，然后再尝试购买产品。
  2. 如果上述方法还是没办法解决，那么有可能是苹果测试服务器宕机或更新服务，等一段时间再尝试。

*  在沙盒环境测试 OK，有没有必要测试线上环境呢？

  如果在沙盒环境测试没有问题，就没有必要测试线上。因为提交 App 审核后，苹果有一套完整的测试机制或者说有更高级的账号确保线上支付正常。当然 App 上架后，也可以去线上用真金白银测试购买产品。

  **特别注意：如果服务端进行收据验证，那么服务端一定要做好验证 URL 地址的切换。一般做法就是不管是沙盒还是生产环境，先去生产环境验证，如果获取的状态码为21007，那么可以去沙盒环境验证。**

![](https://chenxing640.github.io/images/iap/receipt_verification_strategy.png)

*  为什么我的沙盒账号提示不在此地区，请切回本地的应用商店？

  因为沙盒账号在创建时就已经设置好了地区，中国的只能在中国的 App Store 测试。

*  订阅产品和自动续期订阅

  订阅产品需要验证订阅是否过期，自动续费在购买流程上，与普通购买没有区别，主要的区别：”除了第一次购买行为是用户主动触发的，后续续费都是 Apple 自动完成的，一般在要过期的前24小时开始，苹果会尝试扣费，扣费成功的话，在 App 下次启动的时候主动推送给 App“。 

```
// 订阅特殊处理
if (transaction.originalTransaction) {  
    // 如果是自动续费的订单 originalTransaction 会有内容 
} else {
    // 普通购买，以及第一次购买自动订阅
}
```

*  刷单问题

  验证接收到响应信息，一定要比对 Product Identifier、Bundle Identifier、User Identifier、Transaction Identifier 等信息，防止冒用其他收据信息领取产品，还有防止利用外汇汇率差的刷单问题。

*  漏单问题

  一般来说，对于消耗性商品，我们用得最多的是在判断用户购买成功之后交给我们的服务器进行校验，收到服务器的确认后把支付交易 finish 掉。

```
// finish 支付交易
SKPaymentQueue.default().finishTransaction(transaction)
```

  如果不把支付交易 finish 掉的话，就会在下次重新打开应用且代码执行到监听内购队列后，此方法 `public func paymentQueue(_ queue: SKPaymentQueue, updatedTransactions transactions: [SKPaymentTransaction])` 都会被回调，直到被 finish 掉为止。所以为了防止漏单，建议将内购类做成单例，并在程序入口启动内购类监听内购队列。这样做的话，即使用户在成功购买商品后，由于各种原因没告知服务器就关闭了应用，在下次打开应用时，也能及时把支付交易补回，这样就不会造成漏单问题了。

  但事与愿违，在调试中，我们发现如果在有多个成功交易未 finish 掉的情况下，把应用关闭后再打开，往往会把其中某些任务漏掉，即回调方法少回调了，这让我们非常郁闷。既然官方的 API 不好使，我们只能把这个重任交给后台的验证流程了，具体的做法下面会讲到。

*  验证响应信息

```
{
    environment = Sandbox;
    receipt =     {
        "adam_id" = 0;
        "app_item_id" = 0;
        "application_version" = "1.0.3.2";
        "bundle_id" = "**********";
        "download_id" = 0;
        "in_app" =         (
                        {
                "is_trial_period" = false;
                "original_purchase_date" = "2017-02-08 02:26:13 Etc/GMT";
                "original_purchase_date_ms" = 1486520773000;
                "original_purchase_date_pst" = "2017-02-07 18:26:13 America/Los_Angeles";
                "original_transaction_id" = 1000000271607744;
                "product_id" = "**********_06";
                "purchase_date" = "2017-02-08 02:26:13 Etc/GMT";
                "purchase_date_ms" = 1486520773000;
                "purchase_date_pst" = "2017-02-07 18:26:13 America/Los_Angeles";
                quantity = 1;
                "transaction_id" = 1000000271607744;
            },
                        {
                "is_trial_period" = false;
                "original_purchase_date" = "2017-02-25 05:59:35 Etc/GMT";
                "original_purchase_date_ms" = 1488002375000;
                "original_purchase_date_pst" = "2017-02-24 21:59:35 America/Los_Angeles";
                "original_transaction_id" = 1000000276891381;
                "product_id" = "**********_01";
                "purchase_date" = "2017-02-25 05:59:35 Etc/GMT";
                "purchase_date_ms" = 1488002375000;
                "purchase_date_pst" = "2017-02-24 21:59:35 America/Los_Angeles";
                quantity = 1;
                "transaction_id" = 1000000276891381;
            },
                        {
                "is_trial_period" = false;
                "original_purchase_date" = "2017-03-10 05:44:43 Etc/GMT";
                "original_purchase_date_ms" = 1489124683000;
                "original_purchase_date_pst" = "2017-03-09 21:44:43 America/Los_Angeles";
                "original_transaction_id" = 1000000280765165;
                "product_id" = "**********_01";
                "purchase_date" = "2017-03-10 05:44:43 Etc/GMT";
                "purchase_date_ms" = 1489124683000;
                "purchase_date_pst" = "2017-03-09 21:44:43 America/Los_Angeles";
                quantity = 1;
                "transaction_id" = 1000000280765165;
            }
        );
        "original_application_version" = "1.0";
        "original_purchase_date" = "2013-08-01 07:00:00 Etc/GMT";
        "original_purchase_date_ms" = 1375340400000;
        "original_purchase_date_pst" = "2013-08-01 00:00:00 America/Los_Angeles";
        "receipt_creation_date" = "2017-03-10 05:44:44 Etc/GMT";
        "receipt_creation_date_ms" = 1489124684000;
        "receipt_creation_date_pst" = "2017-03-09 21:44:44 America/Los_Angeles";
        "receipt_type" = ProductionSandbox;
        "request_date" = "2017-03-10 08:50:00 Etc/GMT";
        "request_date_ms" = 1489135800761;
        "request_date_pst" = "2017-03-10 00:50:00 America/Los_Angeles";
        "version_external_identifier" = 0;
    };
    status = 0;
}
```

  这里面我们最关心的是 in_app 里的数组，因为根据苹果的官方文档所示，这些就是付款成功而未被 finish 掉的交易，而一般这个数组里只会存在一个元素，这里会出现3个，是因为这3个订单已经被苹果漏掉了，是的，这就是上面所提到的漏单情况，回调方法是不会再走了，恶心吧......

  但生活还是得继续，我们可以看到每个交易里都有一些很详细的信息，一般我们只对 `transaction_id (交易 ID)`、`original_transaction_id (原始交易 ID)`、`product_id (商品 ID)`和 `bundle_id (应用包唯一 ID)`等重要信息感兴趣，服务器也是凭此作为用户购买成功的依据，那么问题来了，这里好像并没有用户 ID，是的，服务器是不知道商品是谁买的，所以我们要把用户 ID 和交易 ID 也一起发给服务器，让服务器与验证返回的数据进行匹对，从而把买家和商品对应起来。

```
// 设置发送给服务器的参数
var param = [String: Any]()
param["receipt"] = receiptBase64
param["userID"] = self.userID
param["transactionID"] = transaction.transactionIdentifier
```

  来到这里，刚才遗留的漏单问题是时候要拿出来解决了，刚才也说到了，回调方法有可能少走，甚至还有可能在客户端启动后完全不走 (这个只是以防万一) 。

  我个人建议的做法是，首先在服务端建立2个表，一个黑表一个白表，黑表是记录过往真正购买成功的历史信息，白表是记录付款成功而未认领的交易信息。在客户端启动后的20秒内 (时间可以自己定) 回调方法如果都没有走，我们就主动把收据等一些信息上传给服务器，当然最好把用户的一些信息，包括账号ID，手机型号，系统版本等信息一并带上，服务器拿到收据后去苹果后台验证，把得到的付款成功的交易信息全部写进白表里 (检测去重)。以后如果有新交易产生，客户端会把收据和交易ID等信息传给服务器，服务器同样到苹果后台验证后写进白表，接着在表里看看是否有客户端所给的交易号信息，如果有再去黑表里检测是否存在，黑表不存在则判断为成功购买并结算商品，这时要在白表中删除对应数据和在黑表中添加新数据，之后回馈给客户端，客户端把交易 finish 掉，这个购买流程就算是结束了。这时候白表里记录着的很有可能就是一些被漏掉的订单，为什么不是一定而是很有可能？ 因为会存在已经记录在黑表中但未被客户端 finish 掉的订单，此时再到黑表中滤一遍就知道是否是真正的漏单了，这时候只能通过人工的方式去解决了，比如可以主动跟这位用户沟通询问情况，或者是在有用户反应漏单时，可以在表中检测相关信息判断是否属实等等。另外服务器可以定时检测两个表中的数据进行去重操作，当然也可以在每次添加进白表前先在黑表中过滤，不过这样比较耗性能。如果有更好的想法，希望大家可以在评论区写下提示或者思路。

*  误充问题

  关于这个问题还是挺有趣的，因为存在这样的一种情况：用户A登录后买了一样商品，但与服务器交互失败了，导致没有把交易信息告知服务器，接着他退出了当前帐号，这时候用户B来了，一登录服务器，我们就会用当前用户 ID 把上次没有走完的内购逻辑继续走下去，接下来的事情相信大家都能想像到了，用户B会发现他获得了一件商品，是的，用户A买的东西被充到了用户B的手上。

  要解决这个问题必须要把交易和用户 ID 绑定起来，要怎么做呢？其实很简单，我们只要在添加交易队列之前把用户 ID 设进去即可。

```
let payment = SKMutablePayment(product: product)
payment.quantity = quantity
if #available(iOS 7.0, *) {
    payment.applicationUsername = userIdentifier
}
SKPaymentQueue.default().add(payment)
```

  然后给服务器发送的参数就不再像之前那样写了。

```
// 设置发送给服务器的参数
var param = [String: Any]()
param["receipt"] = receiptBase64
param["userID"] = transactions.payment.applicationUsername ?? ""
param["transactionID"] = transaction.transactionIdentifier
```

## 最后

附上 iOS 内购开源库和 Demo 地址，觉得还行呢，麻烦顺手给个star。对内购感兴趣或者正在开发实现内购的小伙伴，希望能够解决你的一些问题。即使不能，也希望能给你提供一些思路。

*  Swift库和Demo地址 =>【[https://github.com/chenxing640/DYFStore](https://github.com/chenxing640/DYFStore)】
*  ObjC库和Demo地址 =>【[https://github.com/chenxing640/DYFStoreKit](https://github.com/chenxing640/DYFStoreKit)】

支持 Pods，在 Podfile 文件中添加：`pod 'DYFStore'` 或者 `pod 'DYFStoreKit'`。

---

总结：写的有不好的地方希望大家指出，我会更正，大家有什么看不明白的，也可以去 GitHub 里面提问，我会尽力解答。
