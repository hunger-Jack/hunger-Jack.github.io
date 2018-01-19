---
title: html2canvas截图
copyright: true
date: 2018-01-12 02:14:18
categories: 
- js工具
tags:
- html2canvas截图
---
### 使用html2canvas截图
1. 进入[官网](http://html2canvas.hertzen.com/)，可以看到如下截图，点开箭头位置文件，在自己项目下建立一个js文件，把里面内容拷贝进去。
![Snip20180112_12.png](https://i.loli.net/2018/01/12/5a57aa8755285.png)
<!--more-->
2. 在你想要截图的页面添加一个按钮，添加click事件，写入以下代码：
~~~js
html2canvas(document.querySelector("#capture")).then(canvas => {
    document.body.appendChild(canvas)//#capture 是你想截图容器id或class，会把次id或class里面的内容都截图
});
~~~
3. 如果想点击之后直接下载，可以使用file-saver插件，先安装此插件：
~~~
npm install file-saver -g
~~~
4. 修改js代码：
~~~js
html2canvas(document.querySelector('#resumePreview')).then(function (canvas) {
  canvas.toBlob(function (blob) {
    FileSaver.saveAs(blob, 'Resume.png')//‘Resume.png’可以自己修改
  })
})
~~~
[vue在线简历制作](http://www.jirengujack.cn/vue-resume/dist/#/) 这个是我自己的第一个稍微完整的一个小项目，在不断赶紧中，多多指点。在预览之后左上角会有截图按钮哦，
点击按钮之后会自动下载。
![Snip20180112_15.png](https://i.loli.net/2018/01/12/5a57aef8ae021.png)