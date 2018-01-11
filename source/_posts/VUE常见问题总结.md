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

### npm run build不成功

使用vue-cli，并且使用router的时候，build之后页面没有任何报错，但是页面出现空白，可能有两个原因：

1. build配置路径错误
2. router使用了‘history’

<!--more-->

解决方法：
1. 在config/inde.js找到以下代码，修改assetsPulicPath的路径
~~~js
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
    举个例子：例如本地的整个项目名称为resume，build生成dist的路径为resume/dist，通过github pages来展示页面，在github里面，本地的resume对应远程仓库名称为resume，github pages页面提供的网址是yourname/github.io/resume，那么这里的路径就要改成/resume/dist/.
2. 打开src自己配置的router文件，里面的mode: 'history'删除了就可以了。