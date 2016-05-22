---
title: 我的Swift学习之路(七)--多线程编程
date:  2016-05-22 00:33:22
tags:
    - Swift
---


>在日常iOS开发中，如遇到需要另开线程操作程序，想到的第一个就是GCD（共产党?!!），**Why**？好用嘛,官方指定使用产品。好了废话不多说，接下来就来介绍下这个B

## GCD 核心概念

1. 将`任务`添加到`队列`，并且指定`执行任务的函数`
2. 任务使用 `closure` 封装
    * 任务的 `closure` 没有参数也没有返回值
3. 执行任务的函数
    * 异步 `dispatch_async`
        * 不用等待当前语句执行完毕，就可以执行下一条语句
        * 会开启线程执行 `closure` 的任务
        * `异步`是多线程的代名词
    * 同步 `dispatch_sync`
        * 必须等待当前语句执行完毕，才会执行下一条语句
        * 不会开启线程
        * 在当前执行 `closure` 的任务
4. 队列 - 负责调度任务
    * 串行队列
        * 一次只能"调度"一个任务
        * `dispatch_queue_create("MedClipper", nil)`
    * 并发队列
        * 一次可以"调度"多个任务
        * dispatch_queue_create("MedClipper", DISPATCH_QUEUE_CONCURRENT)
    * 主队列
        * 专门用来在主线程上调度任务的队列
        * 不会开启线程
        * **在`主线程空闲时`才会调度队列中的任务在主线程执行**
        * `dispatch_get_main_queue()`

## - 队列的选择

* 多线程的目的：将耗时的操作放在后台执行！

* 串行队列，只开一条线程，所有任务顺序执行
    * 如果任务有先后执行顺序的要求
    * 效率低 -> 执行慢 -> "省电"
    * 有的时候，用户其实不希望太快！例如使用 3G 流量，"省钱"
* 并发队列，会开启多条线程，所有任务不按照顺序执行
    * 如果任务没有先后执行顺序的要求
    * 效率高 -> 执行快 -> "费电"

### 实际开发中，线程数量如何决定?

* WIFI 线程数 `6` 条
* 3G / 4G 移动开发的时候，`2~3`条，再多会费电费钱！

###  GCD 最常用代码组合！
```Swift
dispatch_async(dispatch_get_global_queue(0, 0)) { 
	  print("do something on background")
	
	  dispatch_async(dispatch_get_main_queue(), {
	      print("update UI thread")
	  })
 }
```

## Barrier 异步

>在并行队列执行任务中,如果想让某一个任务先执行完后再执行其后面的任务，此时可以用dispatch_barrier_async


> 使用 `dispatch_barrier_async` 添加的 block 会在之前添加的 block 全部运行结束之后，才在同一个线程顺序执行，从而保证对非线程安全的对象进行正确的操作！

## Barrier 工作示意图

![](/images/Dispatch-Barrier.png)

> 注意：`dispatch_barrier_async` 必须使用自定义队列，否则执行效果和全局队列一致

##代码演练
```Swift
let asyncQueue = dispatch_queue_create("test_barrier", DISPATCH_QUEUE_CONCURRENT)
	dispatch_async(asyncQueue) {
	  print(1)
	  NSThread.sleepForTimeInterval(2)
	}
	dispatch_barrier_async(asyncQueue) {
	  print(2)
	}
	dispatch_async(asyncQueue) {
	  print(3)
		}
}
```

>打印结果： 
>1
>2
>3


##	Apply

>dispatch_apply函数是dispatch_sync函数和Dispatch Group的关联API,该函数按指定的次数将指的Block追加到指定的Dispatch Queue中,并等到全部的处理执行结束 

```Swift
dispatch_apply(3, dispatch_get_global_queue(0, 0)) { (time) in
		print(time)
}
```

>打印结果： 
>1
>0
>2

## Gruop

>GCD中的调度组，实际开发中很常用，如同步数据过程中，使用调度组来控制多个线程的工作是必不可少的

```Swift
let group = dispatch_group_create()
let asyncQueue = dispatch_get_global_queue(0, 0)

dispatch_group_async(group, asyncQueue) { 
  print("mission A")
}
dispatch_group_async(group, asyncQueue) {
  print("mission B")
}

dispatch_group_notify(group, dispatch_get_main_queue()) { 
  print("two mission over")
}
```

## 小结

* 开不开线程由`执行任务的函数`决定
    * `异步`开，`异步`是多线程的代名词
    * `同步`不开
* 开几条线程由`队列`决定
    * `串行队列`开一条线程
    * `并发队列`开多条线程，具体能开的线程数量由底层线程池决定
        * iOS 8.0 之后，GCD 能够开启非常多的线程

>总之，GCD是一个替代诸如NSThread等技术的很高效和强大的技术。GCD完全可以处理诸如数据锁定和资源泄漏等复杂的异步编程问题。


