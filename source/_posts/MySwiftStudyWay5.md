---
title: 我的Swift学习之路(五)--面向对象
tags:
    - Swift
---


# 目标

* 构造函数
    * 构造函数的基本概念
    * 构造函数的执行顺序
    * `KVC` 在构造函数中的使用及原理
    * 便利构造函数
    * 析构函数
    * 区分 `重载` 和 `重写`
* 懒加载
* 只读属性（计算型属性）

# 构造函数基础

> `构造函数`是一种特殊的函数，主要用来在创建对象时初始化对象，为对象`成员变量`设置初始值，在 OC 中的构造函数是 initWithXXX，在 Swift 中由于支持函数**重载**，所有的构造函数都是 `init`

构造函数的作用

* 分配空间 `alloc`
* 设置初始值 `init`

## 必选属性

* 自定义 `Person` 对象

```swift
class Person: NSObject {

    /// 姓名
    var name: String
    /// 年龄
    var age: Int
}
```

提示错误 `Class 'Person' has no initializers` -> `'Person' 类没有实例化器s`

原因：如果一个类中定义了必选属性，必须通过构造函数为这些必选属性分配空间并且设置初始值

* `重写` 父类的构造函数

```swift
/// `重写`父类的构造函数
override init() {

}
```

提示错误 `Property 'self.name' not initialized at implicitly generated super.init call` -> `属性 'self.name' 没有在隐式生成的 super.init 调用前被初始化`

* 手动添加 `super.init()` 调用

```swift
/// `重写`父类的构造函数
override init() {
    super.init()
}
```

提示错误 `Property 'self.name' not initialized at super.init call` -> `属性 'self.name' 没有在 super.init 调用前被初始化`

* 为比选属性设置初始值

```swift
/// `重写`父类的构造函数
override init() {
    name = "张三"
    age = 18

    super.init()
}
```

### 小结

* 非 Optional 属性，都必须在构造函数中设置初始值，从而保证对象在被实例化的时候，属性都被正确初始化
* 在调用父类构造函数之前，必须保证本类的属性都已经完成初始化
* Swift 中的构造函数不用写 `func`

## 子类的构造函数

* 自定义子类时，需要在构造函数中，首先为本类定义的属性设置初始值
* 然后再调用父类的构造函数，初始化父类中定义的属性

```swift
/// 学生类
class Student: Person {

    /// 学号
    var no: String

    override init() {
        no = "001"

        super.init()
    }
}
```

### 小结

* 先调用本类的构造函数初始化本类的属性
* 然后调用父类的构造函数初始化父类的属性
* Xcode 7 beta 5之后，父类的构造函数会被自动调用，强烈建议写 `super.init()`，保持代码执行线索的可读性
* `super.init()` 必须放在本类属性初始化的后面，保证本类属性全部初始化完成

## `Optional` 属性

* 将对象属性类型设置为 `Optional`

```swift
class Person: NSObject {
    /// 姓名
    var name: String?
    /// 年龄
    var age: Int?
}
```

* `可选属性`不需要设置初始值，默认初始值都是 nil
* `可选属性`是在设置数值的时候才分配空间的，是延迟分配空间的，更加符合移动开发中延迟创建的原则

# convenience 便利构造函数

* 默认情况下，所有的构造方法都是指定构造函数 `Designated`
* `convenience` 关键字修饰的构造方法就是便利构造函数
* 便利构造函数具有以下特点：
    * 可以返回 `nil`
    * 只有便利构造函数中可以调用 `self.init()`
    * 便利构造函数不能被`重写`或者 `super`


```swift
/// `便利构造函数`
///
/// - parameter name: 姓名
/// - parameter age:  年龄
///
/// - returns: Person 对象，如果年龄过小或者过大，返回 nil
convenience init?(name: String, age: Int) {
    if age < 20 || age > 100 {
        return nil
    }

    self.init(dict: ["name": name, "age": age])
}
```

> 注意：在 Xcode 中，输入 `self.init` 时没有智能提示


```swift
/// 学生类
class Student: Person {

    /// 学号
    var no: String?

    convenience init?(name: String, age: Int, no: String) {
        self.init(name: name, age: age)

        self.no = no
    }
}
```

## 便利构造函数应用场景

* 根据给定参数判断是否创建对象，而不像指定构造函数那样必须要实例化一个对象出来
* 在实际开发中，可以对已有类的构造函数进行扩展，利用便利构造函数，简化对象的创建


## 构造函数小结

* 指定构造函数必须调用其直接父类的的指定构造函数（除非没有父类）
* 便利构造函数必须调用同一类中定义的其他`指定构造函数`或者用 `self.` 的方式调用`父类的便利构造函数`
* 便利构造函数可以返回 `nil`
* 便利构造函数不能被继承


# KVC 字典转模型构造函数

```swift
/// `重写`构造函数
///
/// - parameter dict: 字典
///
/// - returns: Person 对象
init(dict: [String: AnyObject]) {
    setValuesForKeysWithDictionary(dict)
}
```

* 以上代码编译就会报错！
* 原因：
    * KVC 是 OC 特有的，KVC 本质上是在`运行时`，动态向对象发送 `setValue:ForKey:` 方法，为对象的属性设置数值
    * 因此，在使用 KVC 方法之前，需要确保对象已经被正确`实例化`

* 添加 `super.init()` 同样会报错
* 原因：
    * `必选属性`必须在调用父类构造函数之前完成初始化分配工作

* 讲必选参数修改为可选参数，调整后的代码如下：

```swift
/// 个人模型
class Person: NSObject {

    /// 姓名
    var name: String?
    /// 年龄
    var age: Int?

    /// `重写`构造函数
    ///
    /// - parameter dict: 字典
    ///
    /// - returns: Person 对象
    init(dict: [String: AnyObject]) {
        super.init()

        setValuesForKeysWithDictionary(dict)
    }
}
```

> 运行测试，仍然会报错

错误信息：`this class is not key value coding-compliant for the key age.` -> `这个类的键值 age 与 键值编码不兼容`

* 原因：
    * 在 Swift 中，如果属性是可选的，在初始化时，不会为该属性分配空间
    * 而 OC 中基本数据类型就是保存一个数值，不存在`可选`的概念
* 解决办法：给基本数据类型设置初始值
* 修改后的代码如下：

```swift
/// 姓名
var name: String?
/// 年龄
var age: Int? = 0

/// `重写`构造函数
///
/// - parameter dict: 字典
///
/// - returns: Person 对象
init(dict: [String: AnyObject]) {
    super.init()

    setValuesForKeysWithDictionary(dict)
}
```

> 提示：在定义类时，基本数据类型属性一定要设置初始值，否则无法正常使用 KVC 设置数值

## KVC 函数调用顺序

```swift
init(dict: [String: AnyObject]) {
    super.init()

    setValuesForKeysWithDictionary(dict)
}

override func setValue(value: AnyObject?, forKey key: String) {
    print("Key \(key) \(value)")

    super.setValue(value, forKey: key)
}

// `NSObject` 默认在发现没有定义的键值时，会抛出 `NSUndefinedKeyException` 异常
override func setValue(value: AnyObject?, forUndefinedKey key: String) {
    print("UndefinedKey \(key) \(value)")
}
```

* `setValuesForKeysWithDictionary` 会按照字典中的 `key` 重复调用 `setValue:forKey` 函数
* 如果没有实现 `forUndefinedKey` 函数，程序会直接崩溃
    * NSObject 默认在发现没有定义的键值时，会抛出 `NSUndefinedKeyException` 异常
* 如果实现了 `forUndefinedKey`，会保证 `setValuesForKeysWithDictionary` 继续遍历后续的 `key`
* 如果父类实现了 `forUndefinedKey`，子类可以不必再实现此函数

## 子类的 KVC 函数

```swift
/// 学生类
class Student: Person {

    /// 学号
    var no: String?
}
```

* 如果父类中已经实现了父类的相关方法，子类中不用再实现相关方法

# 懒加载

> 在 iOS 开发中，懒加载是无处不在的

* 懒加载的格式如下：

```swift
lazy var person: Person = {
    print("懒加载")
    return Person()
}()
```

* 懒加载本质上是一个闭包
* 以上代码可以改写为以下格式

```swift
let personFunc = { () -> Person in
    print("懒加载")
    return Person()
}
lazy var demoPerson: Person = self.personFunc()
```

* 懒加载的简单写法

```swift
lazy var demoPerson: Person = Person()
```
