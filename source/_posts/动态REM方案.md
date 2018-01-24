---
title: 动态REM方案
copyright: true
date: 2018-01-18 00:57:25
categories:
- web移动端
- 动态REM
tags:
- 动态REM
---
# 动态REm方案
## 浏览器禁止980px缩放
使用`<meta name="viewport" content="user-scalable=no"`禁止浏览缩放。我自己测试的结果是，只要meta的content里面有'initial-scale=1/2/3'、'width=device-width'或者'user-scalable=no',页面都不能缩放。
如果同时写minimum-scale和maximum-scale=1了，最大和最小缩放都是1，那肯定不支持缩放了。

~~~html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,minimum-scale=1">
</head>
<body>
    //内容
</body>
</html>
~~~
<!--more-->
## 设置html的font-size
设置`html{font-size:页面宽度/10}`，`font-size`的值不能太小，因为浏览器有默认最小字体，Chrome的是12px；也不能设置太大，太大会导致你页面的数字都会很小。一般我们都是使用js来动态的设置`font-size`。

~~~javascript
var clientWidth = document.documentElement.clientWidth
document.write(`<style>html{font-size:${clientWidth/10}px}</style>`)
//如果这样写，这一段代码最好卸载`<head>`标签里面
~~~

## 如何在页面使用
这样一来，在meta中width=content-width的前提条件下，例如获取的clientWidth=375px，font-size=37.5px，1rem=37.5px如果想让一个元素的宽度占满整个屏幕，就可以把它的宽度设为10rem即可。
rem就是一个相对单位，相对于clientWidth，如果clientWidth变化，所有使用rem作为单位的元素都会按照比例变化，布局不会错乱。

## 使用less更方便
有个问题，这样每次都要手动去计算对应的比例，例如需要24px，就要用计算器算一下24/37.5,如果需要88px，就要再次计算88/37.5...很麻烦。所以使用less就很方便了。

~~~less
/* 
 * 此处 @r 具体数值根据设计图尺寸而定
 * flexible中设置的标准是【fontSize=16px, when 屏幕宽度=375】，因此，按此标准进行计算，
 * 若设计图为iPhone6(375*667)的二倍稿，则@r设置为32rem
 *
 */ 
@r:32rem;//设置一个常量
width: 80/@r;
height: 80/@r;
/*
 *这里相当于80/@r === 80/32rem === 80/32*font-size === 80/32*(clientWidth/10)
 *如果想把宽度设置为100%，就可以设为320/@r;
 */
~~~

# 一些术语
在网上看了很多，说的有些很难理解，我就自己解释一下更容易看懂，防止以后忘了还要去找，再一次去理解，先解释一下meta标签里面的内容。

`  <meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">`

1. <font style="color:red;font-weight:700">viewport</font>
`viewport`在pc端指的就是浏览器能看到的区域的大小，包括滚动条，不包括浏览器工具栏。HTML元素大小就是根据viewport来说的，HTML宽度的100%就是相当于viewport的100%。
在移动端，viewport默认的宽度是980px，

2. <font style="color:red;font-weight:700">width</font>
`content`中的width值得手机端的视觉视口，也就是手机屏幕的宽度，`width`就是你自己的手机屏幕的宽度。
如果值设为`device-width`，就相当于把理想视口大小设为视觉视口大小，也相当于缩放比例为1了。

3. <font style="color:red;font-weight:700">device-width</font>
`device-width`指的是理想视口，是相对于手机的dpr和设备像素来说的。

4. <font style="color:red;font-weight:700">设备像素</font>
`设备像素`，每一个手机的设备像素都是固定的，例如iPhone6的是750px，iPhone4的是640px。

5. <font style="color:red;font-weight:700">dpr</font>
`dpr`指的是手机的设备像素比，这个值也是固定的，例如iPhone4、iPhone6的是2，Galaxy S5的是3。
	* **公式前提：缩放比例为1。**
	* **dpr = 设备像素大小 / 理想视口大小（css像素大小）**  *//使用宽度或高度计算都可*以
	* **缩放操作的都是理想视口大小，也就是css像素。**


6. <font style="color:red;font-weight:700">user-scalable</font>
`user-scalable`指的是否支持双击缩放，如果值设为'no'就是不支持，设为'yes‘就是支持

7. <font style="color:red;font-weight:700">initial-scale</font>
`initial-scale`指的是默认的缩放比例，最好使用js动态控制，解决1px border问题

7. <font style="color:red;font-weight:700">maximum-scale</font>
`maximum-scale`指的是最大的缩放比例，最好使用js动态控制，解决1px border问题

7. <font style="color:red;font-weight:700">minimum-scale</font>
`minimum-scale`指的是最小的缩放比例，最好使用js动态控制，解决1px border问题


