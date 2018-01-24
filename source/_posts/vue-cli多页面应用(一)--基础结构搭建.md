---
title: vue-cli多页面应用(一)--基础结构搭建
copyright: true
date: 2018-01-24 01:48:15
categories:
- vue
- vue-cli
tags:
- vue-cli
- vue
- webpack
---
## 全局安装vue官方提供的vue-cli脚手架工具
~~~
$ npm install -g vue-cli
~~~

## 创建项目模板，官方提供了5个模板，webpack、webpack-simple、browserify、browserify-simple、simple，我们使用的是webpack模板，初始化项目。
~~~
$ vue init webpack <project-name> //前提安装了了vue
~~~

## 初始化过程中的过程中会有些选项
1. vue build选择runtime+compile，运行时加编译。
2. 安装vue-router，ESLint、Karma+Mocha、Nightwatch根据需求选择安装。
![Jietu20180123-021221.png](https://i.loli.net/2018/01/23/5a6629c66cf1d.png)
3. 初始化完成之后会提示如果进入开发环境，一般是`npm run dev`
<!--more-->
## 初始化之后是一个单页面的应用，我们要调整一下目录结构，使之成为多页面应用
![03.png](https://i.loli.net/2018/01/23/5a662b9bf20e7.png)

1. 在开发目录src下创建modules和pages文件夹，分别存放模板文件和页面文件。
2. 把有关页面的文件放在同一个文件夹方便统一管理。项目有几个页面就可以在pages下面创建几个文件。例如把所有首页内容都在index文件夹里面：
	把index.html(页面模板)，main.js(页面入口文件)，App.vue(页面使用组件，公共组件	放在components文件夹下)，assets(静态资源)，router(路由配置)都放在pages下的	index文件夹下面。把main.js改成index.js，保证页面的入口文件与模板文件名称一致。

## 配置多入口文件和多页面输出
1. 在/build/utils.js中添加两个方法：webpack的多入口文件和多页面输出

~~~js
var path = require('path')
var glob = require('glob')
var HtmlWebpackPlugin = require('html-webpack-plugin')
var PAGE_PATH = path.resolve(__dirname, '../src/pages')
var merge = require('webpack-merge')

//多入口配置
exports.entries = function() {
	//读pages下的所有文件夹的js文件绝对路径，也就是每个页面文件的入口文件
  var entryFiles = glob.sync(PAGE_PATH + '/*/*.js')
  
  var map = {}
  //便利匹配到所有js文件路径，获取所有js文件名称，把对应的名称与路径添加到map对象，最后返回一个map对象，也就是多入口对象
  entryFiles.forEach((filePath) => {
    var filename = filePath.substring(filePath.lastIndexOf('\/') + 1, filePath.lastIndexOf('.'))
    map[filename] = filePath
  })
  return map
}

//多页面输出配置
exports.htmlPlugin = function() {
	//读pages下所有页面文件夹下的所有页面模板路径
  let entryHtml = glob.sync(PAGE_PATH + '/*/*.html')
  let arr = []
  entryHtml.forEach((filePath) => {
    let filename = filePath.substring(filePath.lastIndexOf('\/') + 1, filePath.lastIndexOf('.'))
    let conf = {
      template: filePath,//模板路径
      filename: filename + '.html',//模板文件名
      chunks: [filename],//打包输出文件名字
      inject: true//允许加入静态资源
    }
    if (process.env.NODE_ENV === 'production') {//如果是开发环境，使用此配置
      conf = merge(conf, {
        chunks: ['manifest', 'vendor', filename],
        minify: {
          removeComments: true,//移除注释
          collapseWhitespace: true,//移除空白
          removeAttributeQuotes: true//如果允许，移除属性引号
        },
        chunksSortMode: 'dependency'//模块文件按照依赖排序
      })
    }
    arr.push(new HtmlWebpackPlugin(conf))
  })
  return arr
}
~~~

## 修改webpack入口配置
打开/build/webpack.base.conf.js文件，做如下修改：

~~~js
module.exports = {
	//如果有这句话，急着注释掉，以免出错
	//context: path.resolve(__dirname, '../'),  
	entry: utils.entries(),
...
~~~

## 修改webpack开发环境配置
打开/build/webpack.dev.conf.js文件，做一下修改：

~~~js
  plugins: [
    ......
    //  new HtmlWebpackPlugin({
    //    filename: 'index.html',
    //    template: 'index.html',
    //    inject: true
    //  }),
    ......
  ].concat(utils.htmlPlugin())//注释点以上代码，添加此代码
~~~

## 修改webpack生产环境配置
打开/build/webpack.prod.conf.js文件，做一下修改：

~~~js
  plugins: [
    ......
    // new HtmlWebpackPlugin({
    //   filename: config.build.index,
    //   template: 'index.html',
    //   inject: true,
    //   minify: {
    //     removeComments: true,
    //     collapseWhitespace: true,
    //     removeAttributeQuotes: true
    //   },
    //   chunksSortMode: 'dependency'
    // }),
    ......
  ].concat(utils.htmlPlugin())//注释点以上代码，添加此代码
~~~

	 chunks: ['manifest', 'vendor', filename]
	 关于此代码，我自己的理解是，打包的时候先对vendor模块缓存，再提取公共部分模块，避免把所有的文件都打包

