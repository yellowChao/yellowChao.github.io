---
title: 我的Swift学习之路(三)--循环，集合，字符串
tags:
    - Swift
---

# for 循环

* OC 风格的循环

```swift
var sum = 0
for var i = 0; i < 10; i++ {
    sum += i
}
print(sum)
```

* `for-in`，0..<10 表示从0到9

```swift
sum = 0
for i in 0..<10 {
    sum += i
}
print(sum)
```

* 范围 0...10 表示从0到10

```swift
sum = 0
for i in 0...10 {
    sum += i
}
print(sum)
```

* 省略下标
    * `_` 能够匹配任意类型
    * `_` 表示忽略对应位置的值

```swift
for _ in 0...10 {
    print("hello")
}
```

# 集合

## 数组

* 数组使用 `[]` 定义，这一点与 OC 相同

```swift
//: [Int]
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

* 遍历

```swift
for num in numbers {
    print(num)
}
```

* 通过下标获取指定项内容

```swift
let num1 = numbers[0]
let num2 = numbers[1]
```

* 可变&不可变
    * `let` 定义不可变数组
    * `var` 定义可变数组

```swift
let array = ["zhangsan", "lisi"]
//: 不能向不可变数组中追加内容
//array.append("wangwu")
var array1 = ["zhangsan", "lisi"]

//: 向可变数组中追加内容
array1.append("wangwu")
```

* 数组的类型
    * 如果初始化时，所有内容类型一致，择数组中保存的是该类型的内容
    * 如果初始化时，所有内容类型不一致，择数组中保存的是 `NSObject`

```swift
//: array1 仅允许追加 String 类型的值
//array1.append(18)

var array2 = ["zhangsan", 18]
//: 在 Swift 中，数字可以直接添加到集合，不需要再转换成 `NSNumber`
array2.append(100)
//: 在 Swift 中，如果将结构体对象添加到集合，仍然需要转换成 `NSValue`
array2.append(NSValue(CGPoint: CGPoint(x: 10, y: 10)))
```

* 数组的定义和实例化
    * 使用 `:` 可以只定义数组的类型
    * 实例化之前不允许添加值
    * 使用 `[类型]()` 可以实例化一个空的数组

```swift
var array3: [String]
//: 实例化之前不允许添加值
//array3.append("laowang")
//: 实例化一个空的数组
array3 = [String]()
array3.append("laowang")
```

* 数组的合并
    * 必须是相同类型的数组才能够合并
    * 开发中，通常数组中保存的对象类型都是一样的！

```swift
array3 += array1

//: 必须是相同类型的数组才能够合并，以下两句代码都是不允许的
//array3 += array2
//array2 += array3
```

* 数组的删除

```swift
//: 删除指定位置的元素
array3.removeAtIndex(3)
//: 清空数组
array3.removeAll()
```

* 内存分配
如果向数组中追加元素，超过了容量，会直接在现有容量基础上 * 2

```swift
var list = [Int]()

for i in 0...16 {
    list.append(i)
    print("添加 \(i) 容量 \(list.capacity)")
}
```

## 字典

* 定义
    * 同样使用 `[]` 定义字典
    * `let` 不可变字典
    * `var` 可变字典
    * `[String : NSObject]` 是最常用的字典类型

```swift
//: [String : NSObject] 是最常用的字典类型
var dict = ["name": "zhangsan", "age": 18]
```

* 赋值
    * 赋值直接使用 `dict[key] = value` 格式
    * 如果 key 不存在，会设置新值
    * 如果 key 存在，会覆盖现有值

```swift
//: * 如果 key 不存在，会设置新值
dict["title"] = "boss"
//: * 如果 key 存在，会覆盖现有值
dict["name"] = "lisi"
dict
```

* 遍历
    * `k`，`v` 可以随便写
    * 前面的是 `key`
    * 后面的是 `value`

```swift
//: 遍历
for (k, v) in dict {
    print("\(k) ``` \(v)")
}
```

* 合并字典
    * 如果 key 不存在，会建立新值，否则会覆盖现有值

```swift
//: 合并字典
var dict1 = [String: NSObject]()
dict1["nickname"] = "大老虎"
dict1["age"] = 100

//: 如果 key 不存在，会建立新值，否则会覆盖现有值
for (k, v) in dict1 {
    dict[k] = v
}
print(dict)
```

# 字符串

> 在 Swift 中绝大多数的情况下，推荐使用 `String` 类型

* String 是一个结构体，性能更高
    * String 目前具有了绝大多数 NSString 的功能
    * String 支持直接遍历
* NSString 是一个 OC 对象，性能略差
* Swift 提供了 `String` 和 `NSString` 之间的无缝转换

## 字符串演练

* 遍历字符串中的字符

```swift
for s in str.characters {
    print(s)
}
```

* 字符串长度

```swift
// 返回以字节为单位的字符串长度，一个中文占 3 个字节
let len1 = str.lengthOfBytesUsingEncoding(NSUTF8StringEncoding)
// 返回实际字符的个数
let len2 = str.characters.count
// 返回 utf8 编码长度
let len3 = str.utf8.count
```

* 字符串拼接
    * 直接在 "" 中使用 `\(变量名)` 的方式可以快速拼接字符串

```swift
let str1 = "Hello"
let str2 = "World"
let i = 32
str = "\(i) 个 " + str1 + " " + str2
```

> 我和我的小伙伴再也不要考虑 `stringWithFormat` 了 :D

* 可选项的拼接
    * 如果变量是可选项，拼接的结果中会有 `Optional`
    * 为了应对强行解包存在的风险，苹果提供了 `??` 操作符
    * `??` 操作符用于检测可选项是否为 `nil`
        * 如果不是 `nil`，使用当前值
        * 如果是 `nil`，使用后面的值替代

```swift
let str1 = "Hello"
let str2 = "World"
let i: Int? = 32
str = "\(i ?? 0) 个 " + str1 + " " + str2
```

* 格式化字符串
    * 在实际开发中，如果需要指定字符串格式，可以使用 `String(format:...)` 的方式

```swift
let h = 8
let m = 23
let s = 9
let timeString = String(format: "%02d:%02d:%02d", arguments: [h, m, s])
let timeStr = String(format: "%02d:%02d:%02d", h, m, s)
```

## String & Range 的结合

* 在 Swift 中，`String` 和 `Range`连用时，语法结构比较复杂
* 如果不习惯 Swift 的语法，可以将字符串转换成 `NSString` 再处理

```swift
let helloString = "我们一起飞"
(helloString as NSString).substringWithRange(NSMakeRange(2, 3))
```

* 使用 Range<Index> 的写法

```swift
let startIndex = helloString.startIndex.advancedBy(0)
let endIndex = helloString.endIndex.advancedBy(-1)

helloString.substringWithRange(startIndex..<endIndex)
```
