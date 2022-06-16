---
title: Vue第二天笔记
date: 2018-04-24 17:10:13
tags: Vue
categories:
- 前端框架
---

# 内容回顾

## 动画

### 利用Vue提供好的过渡的类名
	进入阶段
		v-enter
		v-enter-to
		v-enter-active
	
	离开阶段
		v-leave
		v-leave-to
		v-leave-active
		
	注意事项:
		当我们给transition组件设置了name属性，那么我们的v-既要替换成name名称-
<!-- more -->		
### 利用第三方动画库来实现动画
	animate.css
	
	注意事项:
		导入
		在 enter-active-class 和 leave-active-class 中，写上第三方动画库中提供的类名，其中 animated 必须要写，后面再接上具体你需要的动画的类名
		有些情况下，动画要想起作用，必须把它变成块级元素
		
### 利用Vue提供的动画钩子(函数)
	进入
		before-enter
			设置进入开始时候的开始状态/位置
		enter
			设置过渡事件
			刷新帧 offsetWidth offsetHeight
			设置进入时候的结束位置/状态
		after-enter
			后续操作
	
	离开
		before-leave
			设置离开时候的开始状态/位置
		leave
			设置过渡事件
			刷新帧 offsetWidth offsetHeight
			设置离开时候的结束位置/状态
		after-leave
			后续操作

-----------------------

## 计算属性和watch
	计算属性是和data同级，写在computed中，并且computed中都是函数，并且计算属性的函数中，必须要有返回值
	
	好处:缓存
	
	watch:
		监控data中数据的变化

## 组件
	前四种了解，主要是为了看文档
	
	第一种：先定义，再注册，最后使用
	
	第二种：定义注册一步到位，最后使用
	
	第三种，第四种，其实是对组件的template的优化写法

## 品牌管理

------------------------

# 今日课程目标

## 过滤器
	将服务器返回的原始数据，进行过滤之后，再展示出来
	
	局部过滤器
		只能在它自己所在的组件中使用
		
		语法:
			在我们当前组件对象中，和data同级，写一个filters，它是一个对象，里面是函数(过滤器函数)
			
		调用:
			在需要过滤的数据的插值表达式后面，以`|`
			
			{{原始数据 | 过滤器函数名称}}
			
		注意事项:
			我们的过滤器函数，必须至少接收一个参数（就是要过滤的原始数据），然后过滤完成之后，要将结果返回回去
	
	全局过滤器
		在所有的组件中都可以使用
		
		语法:
			在组件注册之前，使用Vue.filter去定义我们的全局过滤器
			
			Vue.filter('过滤器名称',处理函数)
			
		调用:
			和局部过滤器调用是一模一样
			
	局部过滤器优先级高于全局
	
	过滤器的调用顺序如下
		局部过滤器 --> 全局过滤器 -->报错
		
-------------------------

## es6之模版字符串、es6之箭头函数
	好用，但是语法变态
	
	模版字符串
		语法:
			``(键盘1旁边的)  
		
		好处:
			格式化比较方便
			
			占位/替换
			
	箭头函数
		语法:
			const 函数名称 = (形参1,形参2,...) => {
				方法体
			}
	
		注意点:
			1、箭头函数在定义时候不一样，但是在调用的时候是和es3的函数是一模一样的
			
			2、如果形参只有唯一一个，`()`可以省略
			
			3、当我们的方法体中，只有一句话的时候，可以省略 `{}`，如果方法体中有return关键字，必须连同return 一起省略，但是它内部会自动判断要不要返回
			
			4、箭头函数中的this代表的是外部的this，不是代表调用它的this，如果你不太明确我们的箭头函数中的this代表啥，就打印
			
			
	const & let
		const 定义常量不能改，在定义的时候，必须初始化
		
		const & let 支持局部作用域
			{}
			
		const & let没有变量提升
		
	es6语言特性
		严谨
		便捷
	
-------------------------

## Vue中如何发送网络请求
	vue-resource 基于vue
		1、导入vue.js vue-resource.js
		
		2、使用
			参考:https://github.com/pagekit/vue-resource
			https://github.com/pagekit/vue-resource/blob/develop/docs/http.md
			
		GET请求地址:
			http://192.168.26.74:3000/api/login?username=zhangsan&password=123
			
		POST请求地址:
			http://192.168.26.74:3000/api/postLogin
			
		JSONP的请求地址:
			https://api.douban.com/v2/movie/in_theaters
			
		注意：我们在使用jsonp跨域请求豆瓣数据的时候，只需要把get方法换成jsonp就可以啦
	
	axios Vue2.x作者推荐的
		注意:和Vue没有关系，它是一个独立的库，既可以用在Vue中，也可以用在React、Angular中
		
		参考:https://www.awesomes.cn/repo/axios/axios
		
		
	Vue-resource和axios不一样的地方
		vue-resoure是基于Vue的，只能在Vue中使用，而axios不是基于Vue的，可以在其它框架中使用
		
		获取响应的结果时候，Vue-resource是通过response.body获取到的，而axios是通过response.data获取到
	
-------------------------

## 完成品牌管理网络版【了解】
	API说明:
		获取品牌数据:
			http://157.122.54.189:9093/api/getprodlist
			
		新增品牌数据:
			http://157.122.54.189:9093/api/addproduct
			
			需要传递给后台的属性名称是name
			
		删除品牌数据:
			http://157.122.54.189:9093/api/delproduct/225593

	
	1、查询所有的数据，展示
		在Vue实例的生命周期钩子中，发送网络请求
		
		生命周期钩子，是Vue框架提供的，我们只需要实现这些生命周期钩子，Vue在解析我们组件的时候，就会在恰当的时机调用
		
		在created生命周期钩子中，利用axios发送请求，获取到数据之后，赋值给list即可
	
	2、新增
	
	3、删除(动画回顾)
		实现动画(模态窗口要呈现出来)
		删除成功之后
			隐藏模态窗
			重新查询
	
	4、关键字搜索(计算属性)
	
--------------------------

## 路由【掌握】
	实现单页面应用，比如网易云音乐
	
	特点:
		不刷新页面，但是会更新内容
		
	步骤:
		前提:
			导入vue.js和vue-router.js
			
		html:
			写可以点击的链接 	<router-link></router-link>
			写路由占位符/路由出口 <router-view></router-view>
			
		js:
			定义好组件
			创建路由对象，设置路由规则【自动帮我们注册组件】
			把上面创建好的路由对象，注入到根实例中，这样整个应用就拥有路由的功能
			
			
	注意点:
		不要编写边看效果，写完了再去看，否则可能会看到错误

--------------------------

## 安装webpack这个全局包【安装3.x】
	npm i cnpm -g

	npm i webpack@3.11.0 -g

--------------------------