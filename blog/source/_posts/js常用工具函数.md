---
title: js常用工具函数
date: 2019-1-20 11:31:43
tags:
  - 方法总结
categories:
  - HTML
  - JS
---

# js常用工具函数

<!--more-->

# js常用工具函数

1.求区间是否重叠:

> ```
> func isOverlap () {
>     var numargs = arguments.length
>     var max= []
>     var min= []
>     for (i =0 ; i < numargs; i++){ // 获取参数内容。
>         let item =  arguments[i]
>         max.append(item[0])
>         min.append(item[item.length - 1])
>     }
>     var isOverlap = false
>     if(Math.max.apply(null, max)<= Math.min.apply(null, min)){
>         isOverlap = true
>     }   
>     return  isOverlap
> }
> ```

2.







