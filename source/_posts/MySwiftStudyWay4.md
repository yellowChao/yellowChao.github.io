---
title: 我的Swift学习之路(四)--函数
tags:
    - Swift
---

## 目标

* 掌握函数的定义
* 掌握外部参数的用处
* 掌握无返回类型的三种函数定义方式

## 代码实现·

* 函数的定义
    * 格式 `func 函数名(行参列表) -> 返回值 {代码实现}`
    * 调用 `let result = 函数名(值1, 参数2: 值2...)`

```swift
func sum(a: Int, b: Int) -> Int {
    return a + b
}

let result = sum(10, b: 20)
```

* 没有返回值的函数，一共有三种写法
    * 省略
    * ()
    * Void

```swift
func demo(str: String) -> Void {
    print(str)
}
func demo1(str: String) -> () {
    print(str)
}
func demo2(str: String) {
    print(str)
}

demo("hello")
demo1("hello world")
demo2("olleh")
```

* 外部参数
    * 在形参名前再增加一个外部参数名，能够方便调用人员更好地理解函数的语义
    * 格式：`func 函数名(外部参数名 形式参数名: 形式参数类型) -> 返回值类型  { // 代码实现 }`
    *  2.0 中，默认第一个参数名省略

```swift
func sum1(num1 a: Int, num2 b: Int) -> Int {
    return a + b
}

sum1(num1: 10, num2: 20)
```

# 闭包

与 OC 中的 Block 类似，`闭包`主要用于异步操作执行完成后的代码回调，网络访问结果以参数的形式传递给调用方

## 目标

* 掌握闭包的定义
* 掌握闭包的概念和用法
* 了解尾随闭包的写法
* 掌握解除循环引用的方法

### OC 中 Block 概念回顾

* 闭包类似于 OC 中的 `Block`
    * 预先定义好的代码
    * 在需要时执行
    * 可以当作参数传递
    * 可以有返回值
    * 包含 `self` 时需要注意循环引用

# 闭包的定义

* 定义一个函数

```swift
//: 定义一个 sum 函数
func sum(num1 num1: Int, num2: Int) -> Int {
    return num1 + num2
}
sum(num1: 10, num2: 30)

//: 在 Swift 中函数本身就可以当作参数被定义和传递
let mySum = sum
let result = mySum(num1: 20, num2: 30)
```

* 定义一个闭包
    * 闭包 = { (行参) -> 返回值 in // 代码实现 }
    * `in` 用于区分函数定义和代码实现

```swift
//: 闭包 = { (行参) -> 返回值 in // 代码实现 }
let sumFunc = { (num1 x: Int, num2 y: Int) -> Int in
    return x + y
}
sumFunc(num1: 10, num2: 20)
```

* 最简单的闭包，如果没有参数/返回值，则 `参数/返回值/in` 统统都可以省略
    * { 代码实现 }

```swift
let demoFunc = {
    print("hello")
}
```

## 基本使用

## GCD 异步

* 模拟在后台线程加载数据

```swift
func loadData() {
    dispatch_async(dispatch_get_global_queue(0, 0), { () -> Void in
        print("耗时操作 \(NSThread .currentThread())")
    })
}
```

* 尾随闭包，如果闭包是最后一个参数，可以用以下写法
* 注意上下两段代码，`}` 的位置

```swift
func loadData() {
    dispatch_async(dispatch_get_global_queue(0, 0)) { () -> Void in
        print("耗时操作 \(NSThread .currentThread())")
    }
}
```

* 闭包的简写，如果闭包中没有参数和返回值，可以省略

```swift
func loadData() {
    dispatch_async(dispatch_get_global_queue(0, 0)) {
        print("耗时操作 \(NSThread .currentThread())")
    }
}
```

### 自定义闭包参数，实现主线程回调

* 添加没有参数，没有返回值的闭包

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    loadData {
        print("完成回调")
    }
}

// MARK: - 自定义闭包参数
func loadData(finished: ()->()) {

    dispatch_async(dispatch_get_global_queue(0, 0)) {
        print("耗时操作 \(NSThread.currentThread())")

        dispatch_sync(dispatch_get_main_queue()) {
            print("主线程回调 \(NSThread.currentThread())")

            // 执行回调
            finished()
        }
    }
}
```

* 添加回调参数

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    loadData4 { (html) -> () in
        print(html)
    }
}

/// 加载数据
/// 完成回调 - 传入回调闭包，接收异步执行的结果
func loadData4(finished: (html: String) -> ()) {

    dispatch_async(dispatch_get_global_queue(0, 0)) {
        print("加载数据 \(NSThread.currentThread())")

        dispatch_sync(dispatch_get_main_queue()) {
            print("完成回调 \(NSThread.currentThread())")

            finished(html: "<h1>hello world</h1>")
        }
    }
}
```

### 解除循环引用

* 与 OC 类似的方法

```swift
/// 类似于 OC 的解除引用
func demo() {
    weak var weakSelf = self
    tools?.loadData() {
        print("\(weakSelf?.view)")
    }
}
```

* Swift 推荐的方法

```swift
loadData { [weak self] in
    print("\(self?.view)")
}
```
