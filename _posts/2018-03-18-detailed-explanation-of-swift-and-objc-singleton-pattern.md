---
layout: post
title: "Swift 和 Objective-C 单例模式详解"
author: "dgynfi"
date: 2017-06-29
tag: iOS
---


![](https://dgynfi.github.io/images/singleton/singleton_pattern.jpg)


单例模式要求一个类有一个实例，有公开接口可以访问这个实例。单例模式分为以下两种模式：

- **严格单例模式**

  严格单例模式，要求一个类只有一个实例。

- **不严格单例模式**

  不严格单例模式，可以创建多个实例。

有的类只能有一个实例，例如 `UIApplication` 类，通过 `shared` 属性访问唯一的实例，属于严格单例模式。废话不多说，接下来看看 Swift 和 Objective-C 的每种单例模式的具体实现。

### Swift 实现

##### 严格单例模式

大多数 Objective-C 的类都继承自 NSObject，而 Swift 的类可以继承自 NSObject 类或者不继承。

- 继承自 NSObject 类

1. 写法一

```
open class DYFStore: NSObject {
    
    public static let `default` = DYFStore()

    /// Overrides default constructor.
    private override init() {
        super.init()
    }
    
    /// Make sure the class has only one instance.
    open override func copy() -> Any {
        return self
    }
    
    /// Make sure the class has only one instance.
    open override func mutableCopy() -> Any {
        return self
    }
}
```

2. 写法二

```
open class DYFStore: NSObject {
    
    /// A struct named "Inner".
    private struct Inner {
        static var instance: DYFStore? = nil
    }
   
    public class var `default`: DYFStore {
        
        objc_sync_enter(self)
        defer { objc_sync_exit(self) }
        
        guard let instance = Inner.instance else {
            let store = DYFStore()
            Inner.instance = store
            return store
        }
        
        return instance
    }
    
    /// Overrides default constructor.
    private override init() {
        super.init()
    }
     
    /// Make sure the class has only one instance.
    open override func copy() -> Any {
        return self
    }
    
    /// Make sure the class has only one instance.
    open override func mutableCopy() -> Any {
        return self
    }
}
```

3. 写法三

```
open class DYFStore: NSObject {
    
    /// A struct named "Inner".
    private struct Inner {
        static var instance: DYFStore? = nil
    }
   
    public class var `default`: DYFStore {
        
        DispatchQueue.once(token: "com.storekit.DYFStore") {
            if Inner.instance == nil {
                Inner.instance = DYFStore()
            }
        }
        
        return Inner.instance!
    }

    /// Constructs a store singleton with class method.
    ///
    /// - Returns: A store singleton.
    public class func defaultStore() -> DYFStore {
        return DYFStore.self.default
    }

    /// Overrides default constructor.
    private override init() {
        super.init()
    }
     
    /// Make sure the class has only one instance.
    open override func copy() -> Any {
        return self
    }
    
    /// Make sure the class has only one instance.
    open override func mutableCopy() -> Any {
        return self
    }
}

// MARK: - Extends the properties and method for the dispatch queue.
extension DispatchQueue {
    
    /// Declares an array of string to record the token.
    private static var _onceTracker = [String]()
    
    /// Executes a block of code associated with a given token, only once. The code is thread safe and will only execute the code once even in the presence of multi-thread calls.
    ///
    /// - Parameters:
    ///   - token: A unique idetifier.
    ///   - block: A block to execute once.
    public class func once(token: String, block: () -> Void) {
        
        objc_sync_enter(self)
        defer { objc_sync_exit(self) }
        
        if _onceTracker.contains(token) {
            return
        }
        
        _onceTracker.append(token)
        
        block()
    }
    
    /// Submits a task to a dispatch queue for asynchronous execution.
    ///
    /// - Parameter block: The block to be invoked on the queue.
    public func asyncTask(block: @escaping () -> Void) {
        self.async(execute: block)
    }
    
    /// Submits a task to a dispatch queue for asynchronous execution after a specified time.
    ///
    /// - Parameters:
    ///   - time: The block should be executed after a few time delay.
    ///   - block: The block to be invoked on the queue.
    public func asyncAfter(delay time: Double, block: @escaping () -> Void) {
        self.asyncAfter(deadline: .now() + time, execute: block)
    }
}
```

[DYFStore](https://link.jianshu.com?t=https://github.com/dgynfi/DYFStore) (In-app Purchase in Swift for iOS) 属性 default 持有唯一的实例，对外公开。

重载 init() 方法，使其对外不可见，不可以在外部调用，防止在外部创建实例。

重载 copy()、mutableCopy() 方法，返回 self，防止在外部复制实例。这里也可以返回 DYFStore.default，效果是一样的，因为只有一个实例。只有属性 default 能调用 copy()、mutableCopy() 方法，那么 self 就是属性 default。写 self，代码比较简洁。

- 不继承自 NSObject 类

```
open class DYFStore {
    
    public static let `default` = DYFStore()

    /// Privatizes default constructor.
    private init() {}
}
```

不继承自 NSObject 的类没有 copy()、mutableCopy() 方法，不需要重载。其他同上。

##### 不严格单例模式

把重载的 init() 方法去掉，或者把 private 去掉，即可创建多个实例。如果继承自 NSObject，重载 copy()、mutableCopy() 方法：创建新实例，传递数据给新实例，返回新实例。其他与严格单例模式相同。

```
open class DYFStore {
    
    public static let `default` = DYFStore()

    init() {}
}
```

### Objective-C 实现

Objective-C 创建对象的步骤分为以下两步:

- 1、申请内存(alloc)
- 2、初始化(init)

我们要确保对象的唯一性，因此在第一步阶段时我们就要拦截它。

当调用 alloc 方法时，OC 内部会调用 allocWithZone 方法来申请内存，我们覆写这个方法，然后在这个方法中赋值 _instance 并返回单例对象，这样就可以达到我们的目的。

拷贝对象也是同样的原理，覆写copyWithZone方法，然后在这个方法中调用 _instance 返回单例对象，或者禁用 copy 和 mutableCopy 方法 。

##### 严格单例模式

.h 文件

```
@interface DYFStore : NSObject

/** Constructs a store singleton with class method.
 
 @return A store singleton.
 */
+ (instancetype)defaultStore;

/** Disable this method to make sure the class has only one instance.
 */
+ (instancetype)new NS_UNAVAILABLE;

/** Disable this method to make sure the class has only one instance.
 */
- (id)copy NS_UNAVAILABLE;

/** Disable this method to make sure the class has only one instance.
 */
- (id)mutableCopy NS_UNAVAILABLE;

@end
```

.m 文件

```
@implementation DYFStore

// Provides a global static variable.
static DYFStore *_instance = nil;

+ (instancetype)defaultStore {
    return [[self.class alloc] init];
}

/** Returns a new instance of the receiving class.
 */
+ (instancetype)allocWithZone:(struct _NSZone *)zone {
    
    if (_instance == nil) {
        static dispatch_once_t onceToken;
        
        dispatch_once(&onceToken, ^{
            _instance = [super allocWithZone:zone];
        });
    }
    
    return _instance;
}

- (instancetype)init {
    static dispatch_once_t onceToken;
    
    dispatch_once(&onceToken, ^{
        _instance = [super init];
        [_instance setup];
    });
    
    return _instance;
}

/** Sets initial value for some member variables.
 */
- (void)setup {

}

@end
```

在 .h 文件中，用 NS_UNAVAILABLE 禁用初始化和拷贝方法，只允许用 defaultStore 方法访问唯一实例。

静态变量 _instance 持有唯一的实例，通过 defaultStore 方法对外公开。由 dispatch_once 保证 _instance 只初始化一次。方法返回值的 nonnull 表示返回值不为空，这样写方便 Swift 调用。不加 nonnull，defaultStore 方法在 Swift 中的返回值是 optional 类型 (DYFStore?)，不方便使用；若加上 nonnull，则为 [DYFStore](https://github.com/dgynfi/DYFStoreKit) (In-app Purchase in Objective-C for iOS) 类型。

NSObject 的类方法 new 相当于 alloc 和 init 方法。

##### 不严格单例模式

.h 文件

```
@interface DYFStore : NSObject

/** Constructs a store singleton with class method.
 
 @return A store singleton.
 */
+ (instancetype)defaultStore;

@end
```

.m 文件

```
@implementation DYFStore

+ (instancetype)defaultStore {

    static DYFStore *_instance = nil;
    
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        _instance = [[self alloc] init];
    });
    
    return _instance;
}

@end
```

公开的 defaultStore 方法与严格单例模式相同。外部可以通过 init 方法创建与 _instance 不同的实例。

如果重载 copyWithZone: 和 mutableCopyWithZone: 方法，就在里面创建新实例，传递数据给新实例，返回新实例。外部可以通过 copy 或 mutableCopy 方法复制实例。

```
- (id)copyWithZone:(NSZone *)zone {
    DYFStore *store = [[self.class allocWithZone:zone] init];
    // Copy data to store
    return store;
}

- (id)mutableCopyWithZone:(NSZone *)zone {
    DYFStore *store = [[self.class allocWithZone:zone] init];
    // Copy data to store
    return store;
}
```

---

最后，想了解更多详情，请查看我的 Demo，记得给个 Star，😝😝

Demo ( Objective-C )：[戳这里](https://github.com/dgynfi/DYFStoreKit) <br >
Demo ( Swift )：[戳这里](https://github.com/dgynfi/DYFStore)
