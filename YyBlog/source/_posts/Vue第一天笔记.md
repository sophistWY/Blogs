---
title: Vue第一天笔记
date: 2018-04-24 17:10:13
tags: Vue
categories:
- 前端框架
---

# 杂项

# 课程安排
    12天 = 4天基础 + 8天项目
	
<!-- more -->    
# 阿里云
     云服务器 ECS最新版
	https://promotion.aliyun.com/ntms/act/group/team.html?group=IxQOwcJHeS
	
	云虚拟主机
	https://wanwang.aliyun.com/hosting/free?spm=5176.8060947.858673.gongxiangpuhui.250ec835MmatSt
	
	域名【需要备案，下载一个阿里云App，备案方便】
	https://wanwang.aliyun.com/?spm=5176.8076989.765261.255.709b4d25ZhUKwJ
	
	备案需要注意事项
		1、上传个人真实用户信息
		2、个人博客

# Vue12天大致介绍
	4天基础
		MVVM
		指令:大大简化Dom操作
		动画
		watch
		...
		webpack【学会用】
		
	8天项目
		购物商城
		支付

-------------------------

# Vue

## Vue是什么?
	前端三架马车之一(Angular React Vue)
	
	数据驱动
	
	我们Vue2.x

## Vue它能做什么?
	PC端项目：
		网站
		后台管理系统
		
	移动端的项目
		移动Web，WebApp
		Weex 原生应用

## Vue特性
	易用
		已经会了 HTML、CSS、JavaScript？即刻阅读指南开始构建应用！
		
	灵活
		不断繁荣的生态系统，可以在一个库和一套完整框架之间自如伸缩。
		http://www.jq22.com/
		
	高效
		20kB min+gzip 运行大小
		超快虚拟 DOM【提高渲染效率】 
		最省心的优化

## Vue如何学习?
	1、多看文档，文档至少看三遍
		Vue文档地址:https://cn.vuejs.org/v2/guide/
		第一遍，略读(不要求都能看懂，主要是弄明白里面有些啥)
		
		第二遍，精读（用到什么内容，就要精读哪一块）
		
		第三遍，通读+精读（使用Vue做完了一两个项目之后）
		
	2、多做练习
		知识点如果觉得掌握不是很好，多敲两遍
		
	3、看一些大牛写的文章
		阮一峰、知乎专栏、掘金、CSDN、segmentfault
		
	4、学会处理错误【多多提高】
		https://stackoverflow.com/

------------------------

# 今日课程目标

## MVVM
	MVVM 设计模式，和MVC有些像
	
	MVC
		Model（模型）
		
		View（视图） 负责展示
		
		Controller（控制器）
	
	MVVM
		M:Model(模型)
		
		V:View（视图）
		
		VM：视图-模型(相当于控制器)
		
	好处：
		1. 低耦合
		2. 可重用性，达到视图的复用		

## Vue中体现MVVM
	参考:https://cn.vuejs.org/v2/guide/instance.html
	
	步骤:
		1、导入Vue
			拷贝到当前目录，通过相对路径导入
			CDN
			npm
		
		2、写View的代码，在body中，写一个id=app的div，里面写上插值表达式
		
		3、写VM和Model的代码，写在script中
			VM就是一个Vue的实例，参考：https://cn.vuejs.org/v2/guide/instance.html
			
			model写在vm里面，他现在是一个对象的形式去体现的

## 指令【重点】
	作用:简化Dom操作
	
	参考:https://cn.vuejs.org/v2/api/#%E6%8C%87%E4%BB%A4
	
	特点:
		1、都是以v-开头
		2、除了插值表达式，其它都写在标签的属性中

### v-text & v-html
	在视图显示数据
	
	{{}} 和 v-text 只能显示普通文本
	
	{{}} 里面还可以进行一些简单的运算，或是三目表达式，但是不要写太复杂的逻辑，比如if判断，循环不支持
	
	v-html 它可以将模型中字符串中带有的html标签，解析出来

### v-on
	事件处理
	
	注意点:
		如果我们的v-on绑定的事件处理函数，没有参数，在写的时候，可以省略`()`，如果有参数，不能省略
		
		事件还可以加上修饰符【监听键盘按下】
		
		v-on:可以简写成 @

### v-bind
	绑定，当我们的内容是来源于model中的时候，v-bind一般常用于
	src，to 这些属性的标签中
	
	注意：
		1、当我们有些属性的值，不是写死的情况下，就可以考虑使用v-bind:
		2、v-bind: 可以简写成 `:`
		3、v-bind的绑定是单向的，从模型到视图

### v-model
	双向数据绑定
		模型 ---> 视图
		视图 ---> 模型
		
	用于双向数据绑定的标签 input radio checkbox...
	
	应用场景：获取form表单值的时候，非常方便

### v-if & v-show
	特性：要想v-if和v-show起作用，必须给他绑定一个boolean类型的Model
	
	v-if:
		当值为true，dom元素会被创建出来，当值为false的时候
		dom元素被删除
	
	v-show:
		当值为true，dom元素会被创建出来，并且显示(display:block)，当它的值为false的时候，通过样式控制它隐藏(display:none)，但是dom元素还在
		
	实际工作中，如何抉择?
		参考:https://cn.vuejs.org/v2/guide/conditional.html#v-if-vs-v-show
		频繁需要切换的时候，用v-show
		
		不频繁使用用v-if

### v-for
	循环渲染
	
	渲染列表
	
	注意点:
		在遍历的时候，最好给它都加一个唯一标识符 

### v-pre & v-cloak & v-once【了解】
	参考:https://cn.vuejs.org/v2/api/#v-pre

-------------------------

## 其它
	Vue.js devtools 帮助查看Vue中数据的工具
	安装方式:可以通过google插件
	
	注意点:
		1、必须在开发模式下才能使用
		2、有时候不好使，需要把chrome调试台关掉，重新打开

