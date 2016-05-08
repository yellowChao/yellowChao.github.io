---
title: 我的Swift学习之路(六)--条件编译
tags:
    - Swift
---

# 条件编译

>在 C 系语言中，可以使用 #if 或者 #ifdef 之类的编译条件分支来控制哪些代码需要编译，而哪些代码不需要。Swift 中没有宏定义的概念，因此我们不能使用 #ifdef 的方法来检查某个符号是否经过宏定义。但是为了控制编译流程和内容，Swift 还是为我们提供了几种简单的机制来根据需求定制编译内容的。


在我们自己的**MedClipper**项目中使用了很多的条件编译，使得我们在不同的环境下有不同的逻辑处理。

### 首先来看下如何在Swift下如何定义编译条件

我的Swift学习之路(六)--条件编译

![](/images/swiftFlag.png)

>具体步骤:”Build Setting”–>“Swift Compiler-Custom Flags”–>“Other Swift Flags”–>“Debug”中添加所需要的配置选项。如我们想添加常用的DEGUB选项，则可以在此加上”-D DEBUG”。这样我们就可以在代码中来执行一些debug与release时不同的操作


**条件编译**具体项目中的一些使用场景

* 针对集成第三方推送服务各个环境下的配置

```swift
func keyLocalytics() -> String {
        #if PRODUCT
            return keyLocalyticsProduct
        #endif
        #if STAGING_PRO
            Localytics.setLoggingEnabled(true)
            return keyLocalyticsStagingPro
        #endif
        #if STAGING_DEV || BEAT
            Localytics.setLoggingEnabled(true)
            return keyLocalyticsStagingDev
        #endif
    }
```

>条件编译中也可以使用逻辑运算符&&和||以及!，来达到多条件组合


* 在测试环境下显式的展现某种自动触发的功能，方便测试人员和开发人员使用（如:数据同步）

```swift
#if !PRODUCT
	setupAddSyncButtonForTest()
#endif
```

```swift
func setupAddSyncButtonForTest() {
        let syncItem = UIBarButtonItem(title: "Sync", style: UIBarButtonItemStyle.Plain, target: self, action: #selector(PatientListViewController.clickSync))
        self.navigationItem.rightBarButtonItems?.append(syncItem)
    }
```

* 真机测试中以本地服务器作为测试时，自定义当前服务器的IP地址(而不是通过查看网络查看IP地址来手动更改代码中指定的值)

```swift
    func changeIpIfBeta() {
        #if BETA
            let button = UIButton(type: .Custom)
            button.setTitle("CustomIP", forState: .Normal)
            button.setTitleColor(UIColor.blueColor(), forState: .Normal)
            button.titleLabel?.font = UIFont.boldSystemFontOfSize(18)
            button.frame = CGRect(x: 10, y: 20, width: 0, height: 0)
            button.sizeToFit()
            button.addTarget(self, action: #selector(changeIpTapped), forControlEvents: .TouchUpInside)
            self.view.addSubview(button)
        #endif
    }

    #if BETA
    func changeIpTapped() {
        let alert = UIAlertController(title: "更改IP", message: "当前：\(ipAddress)", preferredStyle: .Alert)
        alert.addTextFieldWithConfigurationHandler { (textField) in
            textField.text = ipAddress
        }
        alert.addAction(UIAlertAction(title: "确定", style: .Default) { (action) in
            if let text = alert.textFields?[0].text {
                ipAddress = text
            }
        })
        alert.addAction(UIAlertAction(title: "取消", style: .Cancel) { _ in })
        presentViewController(alert, animated: true, completion: nil)
    }
    #endif
```

* Swift2.0中推出的版本判断

```swift
guard #available(iOS 9, *) else {
    return 
}
//do something in iOS 9
```

> 其中 iOS 9 表示必须在 iOS 9 版本以上才可用，另外一个特性参数：星号（*），表示包含了所有平台，目前有以下几个平台：
> 
* iOS
* iOSApplicationExtension
* OSX
* OSXApplicationExtension
* watchOS
* watchOSApplicationExtension
* tvOS
* tvOSApplicationExtension

>一般来讲，如果没有特殊的情况，都使用 * 表示全平台。

言而总之，总而言之。条件编译能让我们在测试，开发中便利，提高效率，更多的使用场景等待你的挖掘，如果你是一个非常野路子(湖南话)的选手的话 😃

