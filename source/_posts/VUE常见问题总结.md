---
title: VUE常见问题总结
copyright: true
date: 2018-01-10 02:44:54
categories:
- 常见问题总结
- vue-cli
tags:
- 常见问题
- vue
- vue-cli
---

# npm run build不成功

使用vue-cli，并且使用router的时候，build之后页面没有任何报错，但是页面出现空白，可能有两个原因：

1. build配置路径错误
2. router使用了‘history’

<!--more-->

解决方法：

    1. 在config/inde.js找到以下代码，修改assetsPulicPath的路径

~~~
    build: {
        // Template for index.html
        index: path.resolve(__dirname, '../dist/index.html'),

        // Paths
        assetsRoot: path.resolve(__dirname, '../dist'),
        assetsSubDirectory: 'static',
        assetsPublicPath: '/',//在这一行把路径改一下，
            ...//
    }
~~~

*举个例子：例如本地的整个项目名称为resume，build生成dist的路径为resume/dist，通过github pages来展示页面，在github里面，本地的resume对应远程仓库名称为resume，github pages页面提供的网址是yourname/github.io/resume，那么这里的路径就要改成/resume/dist/才可以*

    2. 打开src自己配置的router文件，里面的mode: 'history'删除了就可以了。

# 使用v-for里面的数据动态绑定class
~~~html
<li v-for="(item,index) in resume.skills" :key="index">
	<div v-bind:class="item.Name"></div>
</li> 
~~~
*直接使用v-bind绑定class，引号里面引用v-for里面的数据动态绑定class*

# 使用v-for里面的数据动态绑定style

~~~html
<li v-for="(item,index) in resume.skills" :key="index">
	<div :style="{width:item.Proficiency}"></div>
</li> 
~~~
*直接使用v-bind绑定style，引号里面属性值引用v-for里面的数据即可动态绑定style*

# 使用readonly，可以给input添加click事件
如果使用disabled，给input添加click事件就没有效果。也可以动态控制readonly状态

~~~html
<input @click="alertMessage" :readonly = 'readonly'>
new Vue({
    el: '#app';
    data() {
        return {
            readonly: true || false//这个可以根据具体情况设置boolean值
        }
    }
})
~~~

