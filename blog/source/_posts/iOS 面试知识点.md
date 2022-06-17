
---
title: iOS 面试知识点
date: 2020-08-11 11:31:43
tags:
  - 方法总结
categories:
  - iOS
---

# 关于 iOS 面试知识点

<!--more-->


1.数据结构,算法

冒泡,选择,快排,插入

https://www.linuxidc.com/Linux/2019-04/158184.htm

https://blog.csdn.net/heyuchang666/article/details/49891635

https://blog.csdn.net/yeyazhishang/article/details/82353846

https://www.jianshu.com/p/b37e962a734e

2.设计模式,mvc,mvvm

设计模式:mvc,mvvm,单例,观察者,工厂模式

https://cloud.tencent.com/developer/article/1336232

3.runtime,runloop

runloop : https://www.jianshu.com/p/d260d18dd551

runtime :  https://www.jianshu.com/p/6ebda3cd8052 

> - 动态方法解析  : 让你有机会提供一个函数实现
> - 备用接收者 : 给你把这个消息转发给其他对象的机会
> - 完整消息转发 :`-methodSignatureForSelector:`消息获得函数的参数和返回值,如果返回了一个函数签名，`Runtime`就会创建一个`NSInvocation` 对象并发送 `-forwardInvocation:`消息给目标对象

4.Instruments

> Allocation  Leaks

5.GCD,NSOperation (子线程里面，需要加autoreleasepool?,子线程 runloop,autoreleasepool)

> GCD cpu多核 c写的  dispatch_async  dispatchGroup 
>
> ```objectivec
> dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
>         NSLog(@"666");
>     });
> ```
>
> - `dispatch_suspend`和`dispatch_resume`
> - `dispatch_once`
> - `dispatch_barrier_async`
> - `dispatch_semaphore`
>
> NSOperation: 
>
> NSOperation 是抽象类要使用  NSBlockOperation 和 NSInvocationOperation
>
> 1. `NSOperation`的暂停、恢复和取消 , 设置依赖, 优先级  suspended
>
> GCD是纯C语言的API,NSOperation是基于GCD的OC版本封装
>
> GCD只支持FIFO的队列，NSOperation可以很方便地调整执行顺序，设置最大并发数量
>
> NSOperationQueue可以轻松在operation间设置依赖关系，而GCD需要些很多代码才能实现
>
> NSOperationQueue支持KVO，可以检测operation是否正在执行(isExecuted)，是否结束(isFinisn),是否取消(isCancel)
>
> GCD的执行速度比NSOperation快
>
>

https://www.jianshu.com/p/5593af00c597

6.hybrid app 开发原理

> jscore

7.swift

>

8.工程组件化

>

9.https,http ,tcp/ip,socket (请求头,缓存,窗口滑动等等,3次握手4次挥手)

https详解: https://www.jianshu.com/p/14cd2c9d2cd2

tcp/ip协议: https://segmentfault.com/a/1190000016771604

socket : https://www.cnblogs.com/dolphinX/p/3460545.html

10.静态库,动态库,私有库

https://www.cnblogs.com/junhuawang/p/7598236.html

https://www.jianshu.com/p/1764b67d4527

11.swift ,c,c++混编

https://blog.csdn.net/yaojinhai06/article/details/93329405

12.Apple 官方文档

https://developer.apple.com/documentation

13.weex 工作原理

https://weex.apache.org/zh/guide/introduction.html

14.单元测试

https://www.jianshu.com/p/8bbec078cabe

15.pod原理

https://blog.csdn.net/u014600626/article/details/102922568

16.获取北京网络时间

> ```objectivec
> - (NSDate *)getInternetDate
> {
>      NSString *urlString = @"http://m.baidu.com";
>      urlString = [urlString stringByAddingPercentEscapesUsingEncoding: NSUTF8StringEncoding];
>      NSMutableURLRequest *request = [[NSMutableURLRequest alloc] init];
>      [request setURL:[NSURL URLWithString: urlString]];
>      [request setCachePolicy:NSURLRequestReloadIgnoringCacheData];
>      [request setTimeoutInterval: 2];
>      [request setHTTPShouldHandleCookies:FALSE];
>      [request setHTTPMethod:@"GET"];
>       NSHTTPURLResponse *response;
>      [NSURLConnection sendSynchronousRequest:request returningResponse:&response error:nil];
>  
>      NSString *date = [[response allHeaderFields] objectForKey:@"Date"];
>      date = [date substringFromIndex:5];
>      date = [date substringToIndex:[date length]-4];
>      NSDateFormatter *dMatter = [[NSDateFormatter alloc] init];
>      dMatter.locale = [[NSLocale alloc] initWithLocaleIdentifier:@"en_US"];
>      [dMatter setDateFormat:@"dd MMM yyyy HH:mm:ss"];
> NSDate *netDate = [[dMatter dateFromString:date] dateByAddingTimeInterval:60*60*8];
>     
>     NSTimeZone *zone = [NSTimeZone systemTimeZone];
>     NSInteger interval = [zone secondsFromGMTForDate: netDate];
>     NSDate *localeDate = [netDate  dateByAddingTimeInterval: interval];
>     return localeDate;
> }
> ```

17.iOS 中的各种属性关键字

> 对应基本数据类型默认关键字是：atomic, readwrite, assign。
>
> 对于普通的 Objective-C 对象默认关键字是：atomic, readwrite, strong。
>
> **其实Weak表是一个hash（哈希）表，然后里面的key是指向对象的地址，Value是Weak指针的地址的数组。**
>
> 1、`strong`和`copy`的引用计数器+1特性，体现在给成员变量赋值时，实际调用`objc_storeStrong`方法，在该方法内会做`objc_retain`操作。
> 2、用`copy`修饰的属性，当使用系统的setter方法赋值时，得到的对象是不可变对象。 这一特性是因为`setter`内部不是直接赋值，其根本调用了`reallySetProperty`方法。
> 3、`weak`引用计数器不加1，即在`storeWeak`中并没有objc_retain操作。 每个对象都有一个weak表，存储指向这个对象的所有weak指针。释放时,调用`clearDeallocating`函数。`clearDeallocating`函数首先根据对象地址获取这个weak表，然后遍历这个数组把其中的数据设为nil，最后清理对象的记录。
> 4、`assign`引用计数器不加1，当对象释放后，指向对象的指针会变成野指针，是因为`assign`内部就是直接赋值，没有任何操作。
>
>

18.事件传递

19.kvc,kvo底层原理,Delegate 、Notification和KVO优缺点

20.JSON转Model自己如何实现

21.FMDB多线程问题(数据事务设置)

22.手机架构 i386 , 64位



> 1. 5:07  
> 2. 5:08

