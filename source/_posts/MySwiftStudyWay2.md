---
title: 我的Swift学习之路(二)--控制流
tags:
    - Swift
---

>本期为带来Swift中对程序流程的控制。个人感觉Swift流程控制的代码写法更加简洁方便，而且可读性高更加优美了有没有？=￣ω￣=


## if

* Swift 中没有 C 语言中的`非零即真`概念
* 在逻辑判断时必须显示地指明具体的判断条件 `true` / `false`
* if 语句条件的 `()` 可以省略
* 但是 `{}` 不能省略

```swift
let num = 200
if num < 10 {
    print("比 10 小")
} else if num > 100 {
    print("比 100 大")
} else {
    print("10 ~ 100 之间的数字")
}
```

### 三目运算

* Swift 中的 `三目` 运算保持了和 OC 一致的风格

```swift
var a = 10
var b = 20
let c = a > b ? a : b
print(c)
```


>适当地运用三目，能够让代码写得更加简洁

### 可选项判断

* 由于可选项的内容可能为 `nil`，而一旦为 `nil` 则不允许参与计算
* 因此在实际开发中，经常需要判断可选项的内容是否为 `nil`

#### 单个可选项判断

```swift
let url = NSURL(string: "http://www.baidu.com")

//: 方法1: 强行解包 - 缺陷，如果 url 为空，运行时会崩溃
let request = NSURLRequest(URL: url!)

//: 方法2: 首先判断 - 代码中仍然需要使用 `!` 强行解包
if url != nil {
    let request = NSURLRequest(URL: url!)
}

//: 方法3: 使用 `if let`，这种方式，表明一旦进入 if 分支，u 就不在是可选项
if let u = url where u.host == "www.baidu.com" {
    let request = NSURLRequest(URL: u)
}
```

### 可选项条件判断

```swift
//1:初学 swift 一不小心就会让 if 的嵌套层次很深，让代码变得很丑陋
if let u = url {
    if u.host == "www.baidu.com" {
        let request = NSURLRequest(URL: u)
    }
}

//2:使用 where 关键字，
if let u = url where u.host == "www.baidu.com" {
    let request = NSURLRequest(URL: u)
}
```

* 小结
    * `if let` 不能与使用 `&&`、`||` 等条件判断
    * 如果要增加条件，可以使用 `where` 子句
    * 注意：`where` 子句没有智能提示

#### 多个可选项判断

```swift
//3:可以使用 `,` 同时判断多个可选项是否为空
let oName: String? = "张三"
let oNo: Int? = 100

if let name = oName {
    if let no = oNo {
        print("姓名:" + name + " 学号: " + String(no))
    }
}

if let name = oName, let no = oNo {
    print("姓名:" + name + " 学号: " + String(no))
}
```

#### 判断之后对变量需要修改

```swift
let oName: String? = "张三"
let oNum: Int? = 18

if var name = oName, num = oNum {

    name = "李四"
    num = 1

    print(name, num)
}
```

### guard

* `guard` 是与 `if let` 相反的语法，Swift 2.0 推出的

```swift
let oName: String? = "张三"
let oNum: Int? = 18

guard let name = oName else {
    print("name 为空")
    return
}

guard let num = oNum else {
    print("num 为空")
    return
}

// 代码执行至此，name & num 都是有值的
print(name)
print(num)
```

* 在程序编写时，条件检测之后的代码相对是比较复杂的
* 使用 guard 的好处
    - 能够判断每一个值
    - 在真正的代码逻辑部分，省略了一层嵌套

## switch

* `switch` 不再局限于整数
* `switch` 可以针对`任意数据类型`进行判断
* 不再需要 `break`
* 每一个 `case`后面必须有可以执行的语句
* 要保证处理所有可能的情况，不然编译器直接报错，不处理的条件可以放在 `default` 分支中
* 每一个 `case` 中定义的变量仅在当前 `case` 中有效，而 OC 中需要使用 `{}`

```swift
let score = "优"

switch score {
case "优":
    let name = "学生"
    print(name + "80~100分")
case "良": print("70~80分")
case "中": print("60~70分")
case "差": print("不及格")
default: break
}
```

* switch 中同样能够赋值和使用 `where` 子句

```swift
let point = CGPoint(x: 10, y: 10)
switch point {
case let p where p.x == 0 && p.y == 0:
    print("中心点")
case let p where p.x == 0:
    print("Y轴")
case let p where p.y == 0:
    print("X轴")
case let p where abs(p.x) == abs(p.y):
    print("对角线")
default:
    print("其他")
}
```

* 如果只希望进行条件判断，赋值部分可以省略

```swift
switch score {
case _ where score > 80: print("优")
case _ where score > 60: print("及格")
default: print("其他")
}
```
