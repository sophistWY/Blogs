---
title: Vue第三天笔记
date: 2018-04-24 17:10:13
tags: Vue
categories:
- 前端框架
---

# 内容回顾

## MVVM
	View:视图 	负责呈现数据
	View-Model:视图模型，相当于Controller 负责调度
	Model:模型 负责提供数据
<!-- more -->	
## Vue如何体现MVVM的?
	步骤:
		1、导入vue.js（开发版本）
		
		2、写View的代码，写在body里面的id=app的div中
		
		3、写VM的代码，写在script标签里面，通过 new关键字创建出来的Vue实例就是VM（必须写一个el属性）
		
		4、写Model的代码，模型的代码是放在Vue实例中，里面有一个data属性，里面再写上视图需要的数据
		
## 指令
	作用:简化dom操作
	
	{{}} & v-text & v-html
		都能显示模型数据到视图中
		
		`{{}}`写在标签之内，它能做一些简单的运算
		
		v-text&v-html 都是写在标签的属性中，v-html还可以解析
		模型内容中的html标签
	
	v-on
		<button @事件类型.修饰符="处理函数"></button>
		
		事件类型:原生的事件类型都支持
		处理函数:写在Vue实例的methods属性中（es6中对象里面函数的简写方法）
		
	v-bind & v-model
		v-bind 从模型到视图
		
		v-model 从模型到视图
				从视图到模型
	
	v-if & v-show
		当为true都能看到，当为false的时候，看不到
		
		https://cn.vuejs.org/v2/guide/conditional.html#v-if-vs-v-show
	
	v-for
		<被循环生成的标签 :key="index" v-for="(item,index) in 模型"></被循环生成的标签>
	
	杂项
		v-pre 不编译我们插值表达式
		v-cloak 解决闪烁问题
		v-once 只会渲染一次

----------------------

# 今日课程目标

## 动画
	总结:
		1、被动画的元素，必须被transition标签包裹起来
		
		2、Vue给我们提供好了一些动画的class名称，我们只需要在里面写好需要的css
		
		3、必须在条件渲染 使用 v-if、使用 v-show动画效果才能生效

### 自己根据Vue提供好的class名称，自己写需要的动画css
	参考:https://cn.vuejs.org/v2/guide/transitions.html#%E8%BF%87%E6%B8%A1%E7%9A%84%E7%B1%BB%E5%90%8D
	
	Vue提供的过渡，分为两个阶段，一个是进入，一个是离开

### 在Vue中集成第三方动画库(animate.css)
	enter-active-class：进入时候的动画名称（来自animate.css）
	leave-active-class：离开时候的动画名称（来自animate.css）
	
	注意事项:
		1、如果有时候动画不起作用，要检查一下被动画的元素是否为块级元素
		
		2、使用animate，必须在前面加上animated这个类名

### 通过Vue提供的动画钩子(js函数)
	进入
		before-enter ： 进入之前 设置被动画元素进入的开始位置
		enter：进入中 设置过渡（时间），设置进入结束的位置，刷新动画帧
		after-enter：进入之后，后续的处理
		enter-cancelled：进入取消
		
	离开
		before-leave：离开之前 设置被动画元素的离开的开始位置
		leave：离开中 设置过渡（时间），设置离开结束的位置，刷新动画帧
		after-leave：离开之后，后续处理
		leave-cancelled：离开取消
		
-----------------------

## 计算属性 & watch
	参考:https://cn.vuejs.org/v2/guide/computed.html

	计算属性:
		当需要计算一些东西的时候，使用它比较合适
		
		注意点:
			计算属性是一个函数，写在computed这个对象中，computed是和data同级的
			
			计算属性中依赖的任何一个模型值发生变化，我们的计算属性，就会重新计算，并且将结果返回
			
			计算属性，必须要有return
			
			计算属性有缓存 参考:https://cn.vuejs.org/v2/guide/computed.html#%E8%AE%A1%E7%AE%97%E5%B1%9E%E6%80%A7%E7%BC%93%E5%AD%98-vs-%E6%96%B9%E6%B3%95
		
	watch
		监控模型中的值的变化的时候，可以考虑使用它，比如监控路径变化
		
		注意点：
			可以监控data中模型值的变化，然后给每一个模型值，添加一个侦听器，当模型的值发生改变，就能监控到，并且调用回调函数处理

-----------------------

## 组件【了解】
	组件就是对相关功能代码的封装，以达到“复用”的目的

	Vue组件的五种写法，前四种了解，最后一种掌握
	
	前四种，写在HTML中，主要是为了看懂文档
		第一种：先定义组件，再注册组件，最后使用
		第二种：定义和注册一步到位，最后使用
		第三种：对我们组件的template的优化
			利用Vue提供的template标签
			
		第四种：对我们组件的template的优化
			利用script
	
	最后一种叫做单文件组件.vue结尾的文件【里面有三部分】
	
### 组件的注意事项
	1、组件必须注册之后才能使用
	
	2、组件template中必须要有根元素，一般是一个div
	
	3、组件中的Model是一个函数，并且函数中返回一个新的字面量对象
		https://cn.vuejs.org/v2/guide/components.html#data-%E5%BF%85%E9%A1%BB%E6%98%AF%E5%87%BD%E6%95%B0

-----------------------

## 完成练习(品牌管理内存版)


-----------------------