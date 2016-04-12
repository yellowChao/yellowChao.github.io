---
title: 我的Node.js学习之路(一)--介绍
tags: [Node]
categories: 后端
---

> Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。Node.js使用事件驱动，非阻塞I/O模型，使得它重量轻，效率高。Node.js的package的生态系统:npm是世界开源库的最大的生态系统。现目很多公司如eBay, GoDaddy, Microsoft, PayPal, Uber, Wikipins, Yahoo, 杏树林的MedClipper! 很多项目都迁移到Node.js。


### 环境配置

install homebrew: `ruby -e "$(curl -fsSkL https://raw.github.com/Homebrew/homebrew/go/install)"`

install node/npm: `brew install node`

## Hello World
* 使用命令交互模式

```JavaScript
→ node
> console.log('Hello World')
Hello World
```

* 使用文件名调用

```JavaScript
node helloWorld.js
Hello World
```


## <font color='green'>Node.js的优点</font>

1. 玩`JavaScript`的人多，人多带来了生态圈的繁荣(npm)
2. 前后端语言互通
3. 高并发,非阻塞 I/O

## <font color='red'>Node.js的缺点</font>

1. 不适合CPU密集型应用
2. 开发调试Bug很麻烦(打log)
3. 代码太过灵活



## Node.js服务器和传统服务器对比

{% img /images/threading_node.png %}
![](/images/threading_java.png)


## Node.js适用场景
* RESTfulAPI
* Web开发
* Twitter队列
* 多人聊天室
* OAuth认证
* 游戏数据统计


## Node.js如何处理并发连接
* 多线程、线程池模型
<br/>
![](/images/subsequent1.png)
* 异步、事件驱动模型
<br/>
![](/images/subsequentNode.png)



## I/O
>Node.js解决的另外一个问题是I/O阻塞，看看这样的业务场景：需要从多个数据源拉取数据，然后进行处理。

### 阻塞I/O式代码

```JavaScript
private string ReadTxtToStr(string filename)
{
    //打开文件,打开期间其他代码停止执行，直到完成打开后继续执行代码。
    FileStream fs = File.Open(filename, FileMode.Open);
    Console.WriteLine("loading...");
    StreamReader sr = new StreamReader(fs);
    //读取文件，读取期间其他代码停止执行，直到完成读取后继续执行代码。
    string str=sr.ReadToEnd();
    Console.WriteLine("loading...");
    return str;
}
```


### 非阻塞I/O式代码

```JavaScript
/**
 * Created by yellow on 16/1/12.
 */
//获取文件管理模块
var fs = require("fs")
//使用非阻塞式读取文件内容,所以可能先输出"over"
fs.readFile('input.txt',function(err,data){
    if(err){
        console.error(err)
    }else{
        console.log(data.toString())
    }
})
console.log('over')
```


## 事件循环机制
> 所谓事件循环是指node.js会把所有的异步操作使用事件机制解决，有个线程在不断地循环检测事件队列。node.js中所有的逻辑都是事件的回调函数，所以node.js始终在事件循环中，程序入口就是事件循环第一个事件的回调函数，事件的回调函数中可能会发出I/O请求或直接发射（ emit）事件，执行完毕后返回事件循环。


![](/images/eventLoop.png.png)


## Node.js事件发射器

```JavaScript
//引入events模块
var events = require("events")

//创建eventEmitter 事件触发器对象
var eventsEmitter = new events.EventEmitter()

//创建事件处理程序
var connectHandler = function() {
    console.log("连接成功!")
    //触发data_received事件
    eventsEmitter.emit('data_received')
}

//绑定connection事件
eventsEmitter.on('connection', connectHandler)

//使用匿名函数绑定data_received事件
eventsEmitter.on('data_received', function() {
   console.log("数据接收成功")
})

//触发connection事件
eventsEmitter.emit('connection')
```


## Node.js TCP通信

```JavaScript
// server.js
var net = require('net')

var server = net.createServer(function(socket) {
    socket.on('data', function(data) {
        console.log(data.toString())
        socket.end('88\r\n')
    })
})

server.listen(8000, function() {
  console.log('server start')
})
```



```JavaScript
// client.js
var net = require('net')

var client = net.connect({port: 8000}, function(){
    console.log('client connected')
    client.write('hello world!\r\n')
})
client.on('data', function(data) {
    console.log(data.toString())
    client.end()
})
client.on('end', function() {
    console.log('client disconnected')
})
```


## Web开发

```JavaScript
var express = require('express')
var app = express()

app.get('/', function (req, res) {
  res.send('Hello World!')
})

app.get('/next', function (req, res) {
  res.send('yellow')
})

var server = app.listen(9000, function () {
  var port = server.address().port;
  console.log('my app listening on', port)
})
```


## Javascript 函数式编程

> 在JavaScript语言世界，函数是第一等公民。JavaScript函数是继承自Function的对象,函数能作另一个函数的参数或者返回值使用，这便形成了我们常说的高阶函数



```JavaScript
var R = require('ramda')

var tempArray = [1, 2, 3]
var nextArray = [10, 20, 30]

// print 1
console.log(R.head(tempArray))
debugger
// print 3
console.log(R.add(1, 2))

// print 2 3 4
var addOne = x => (console.log(x + 1))
R.forEach(addOne, [1, 2, 3])

```


## bala bala bala

* [nodeSchool](http://nodeschool.io/)
* 一个让人有学习JavaScript动力的信息 [here](http://blog.jobbole.com/98977/)
