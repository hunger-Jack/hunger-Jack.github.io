---
title: javascript高级程序设计（第3版）之《script元素》
copyright: true
date: 2018-01-15 00:13:17
categories:
- 读书笔记
- JavaScript高级程序设计（第3版）
tags:
- 读书笔记
---

# `<script>`元素属性
   属性       | 定义                                       
   -------- | ---------------------------------------- 
   async    | 【可选】。可以异步加载，表示可以立即下载此脚本，但不影响页面其他操作。**只对外部脚本有效**。 
   charset  | 【可选】。表示通过src属性制定代码的字符集。由于多数浏览器会忽略此属性，很少人使用。 
   defer    | 【可选】。表示脚本可以掩饰到文档完全被解析和显示之后再执行。**只对外部脚本有效。IE7及更早的版本对嵌入脚本也支持此属性。** 
   language | 已废弃。                                     
   src      | 【可选】。表示要执行外部文件的路径或链接。                    
   type     | 【可选】。默认为text/javascript。可以视为language的替代品。表示编写代码使用的脚本语言的内容类型（也成为MIME类型） 
<!--more-->
## defer延迟脚本

  这个属性表明脚本在执行时不会影响页面的构造。也就是说，脚本会被延迟到整个页面都解析完成之后再运行。因此，在`<script>`元素中设置defer属性，就是告诉浏览器此脚本立即下载，但是延迟执行。

  ~~~html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Document</title>
      <script src="example1.js" defer></script>
      <script src="example2.js" defer></script>
  </head>
  <body>
      <!--这里放代码-->
  </script>
  </html>
  ~~~

  这以上例子中，虽然我们把`<script>`元素放在了文档的`<head>`元素中，但其中包含的脚本将延迟到整个`<html>`解析完成之后再执行。由于HTML5规范要求脚本按照他们出现的先后顺序执行，因此第一个延迟脚本会优先于第二个执行，二这两个脚本会优先于DOMContentLoaded事件执行。在实际运用中，两个延迟脚本不一定会按照顺序执行，也不一定会在DOMContentLoaded事件触发前执行，因此最后只包含一个延迟脚本。defer属性很少使用。

## async异步脚本

  这个属性与defer属性蕾西，都用于改变处理脚本的行为。与defer不同的是，使用async的脚本并不能保证按照指定他们的先后属性执行。

  ~~~html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Document</title>
      <script src="example1.js" async></script>
      <script src="example2.js" async></script>
  </head>
  <body>
      <!--这里放代码-->
  </script>
  </html>
  ~~~

  在以上例子中，第二个脚本可能会优先于第一个脚本执行，也不能不是。因此，如果使用async属性，确保两者之后互相不依赖非常重要。指定async属性的目的是不让页面等待两个脚本的下载与执行，而是可以异步的加载页面的其他内容。所以，建议异步脚本不要在加载期间修改DOM，以免出现错误。

  异步脚本一定会在页面load事件前执行，但可能会在DOMContentLoaded事件触发之前或之后执行，因此，使用此属性的人也很少。

> 关于load事件和DOMContentLoaded事件
> DOMContentLoaded事件在load事件之前，dom树渲染完成之后就会触发DOMContentLoaded事件，加载图片等外部资源之后页面所有加载完毕触发load事件

> **在XHTML文档中，要把async属性写完整，设置为async=“async”，defer="defer"**

-------

# `<scrip>`元素嵌入JavaScript代码的用法

## 两种用法

1. 直接在页面内部嵌入JavaScript代码。

  ~~~JavaScript
  <script>
    function sayHello() {
    alert('hello,word')
  }
  </script>
  ~~~

2. 在外部引用JavaScript代码。

  ~~~JavaScript
  <script src="example.js"></script>
  <script src="../example.js"></script>
  <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
  ~~~



## 特别强调

1. 包含在`<script>`元素内部的代码会被从上至下依次解释。所以在元素内部不要出现"`</script>`"字符串，否则会报错。 

  ~~~javascript
  <script>
    function sayHello() {
    alert('</script>')
  }
  </script>

  //Uncaught SyntaxError: Invalid or unexpected token
  //页面只会显示"')}"字符串
  ~~~
  
  ​如果确实要使用"`</script>`"字符串，可以加上转义符'\'。
  
  ~~~javascript
  <script>
    function sayHello() {
    		alert('<\/script>')
  }
  </script>
  ~~~

2. 使用外部引入javascript时，src属性是必须的。这个属性值是一个指向外部JavaScript文件的路径或链接。

  ~~~javascript
  <script src="example.js"></script>
  ~~~

  使用此方式引入JavaScript时，在`<script>`内部不要写任何额外的JavaScript代码。如果写了会被忽略。

  无论使用任何方式，只要不存在defer和async属性，浏览器都会按照`<script>`元素在页面中出现的先后顺序依次解析。所以说，JavaScript代码最好放在`<body>`标签尾部，防止白屏之类问题。

## 两种方法的优缺点
一般情况我们尽可能的引入外部文件，但是这个不是规定，不是所有的文件都必须使用此方式。但是，使用外部文件引入有以下优点：

1. 可维护性：把所有的JavaScript文件都统一放在一个文件夹下，开发人员可以在不触及HTML的情况下编辑维护JavaScript代码。
2. 可缓存：浏览器可以根据设置缓存所有外部的JavaScript文件。如果不同页面会使用同一个文件，那么此文件就只需要下次一次即可，会加快页面的加载速度。
3. 适应未来：通过外部文件来包含JavaScrip无需使用注释hack。HTML和XHTML引入文件的方式相同。

# 文档模式

## 什么是DOCTYPE

DCOTYPE，英文是Document Type Declaration (文档类型声明)。通常情况下，DOCTYPE位于HTML文档最前面的位置，HTML根元素起始标签之前。这样一来，浏览器在解析HTML文档之前就可以确定文档类型，从而决定使用哪种渲染模式（不同的渲染模式回影响浏览器对CSS的渲染甚至JavaScript脚本的解析）。

## 文档类型与渲染模式

目前浏览器的排版引擎有三种模式：混杂模式(Quirks mode)、接近标准模式(Almost standards mode)、标准模式(Standards mode)。
1. 对于标准模式，可以通过以下任意一种文档类型开启：
	
	~~~html
	<! -- HTML 5 -- >
	<!DOCTYPE html>
	
	<! -- HTML 4.01 严格型 -- >
	<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" 
	"http://www.w3.org/TR/html4/strict.dtd">
	
	<! -- XHTML 1.0 严格型 -- >
	<!DOCTYPE html PUBLIC 
	"-//W3C//DTD XHTML 1.0 Strict//EN" 
	"http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
	~~~
2. 对于接近标准模式，可以通过使用过渡型(transitional)或框架集型(frameset)文档当类型来开启：

	~~~html
	<! -- HTML 4.01 过渡型 -- >
	<!DOCTYPE HTML PUBLIC 
	"-//W3C//DTD HTML 4.01 Transitional//EN" 
	"http://www.w3.org/TR/html4/loose.dtd">

	<! -- HTML 4.01 框架集型 -- >
	<!DOCTYPE HTML PUBLIC 
	"-//W3C//DTD HTML 4.01 Frameset//EN" 
	"http://www.w3.org/TR/html4/frameset.dtd">
	
	<! -- XHTML 1.0 过渡型 -- >
	<!DOCTYPE html PUBLIC 
	"-//W3C//DTD XHTML 1.0 Transitional//EN" 
	"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
	
	<! -- XHTML 1.0 框架集型 -- >
	<!DOCTYPE html PUBLIC 
	"-//W3C//DTD XHTML 1.0 Frameset//EN" 
	"http://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd">
	~~~
3. 除了以上文档类型以为的都会触发混杂模式

标准模式与接近标准模式非常接近，从名字上都可以看得出来，他们之间的差异几乎可以忽略不计，因此当有人提交“标准模式”时，指的可能是其他的任意一种。而且，检测文档模式时也不会发现有什么不同。


