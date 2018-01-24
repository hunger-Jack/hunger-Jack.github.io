---
title: git本地关联远程仓库
copyright: true
date: 2018-01-20 02:30:19
categories:
- 常见问题总结
- git
tags:
- git init
- git
---

# 初始化本地仓库
~~~
$ git init
~~~
执行以上代码之后，会发现当前文件下多了一个`.git`的文件，这个是用户管理跟踪本地版本库的，千万不要去乱动，不然就乱了可就麻烦了。

# 在github创建一个远程仓库
![QQ20180120-023559@2x.png](https://i.loli.net/2018/01/20/5a623aa9edfea.png)
![2.png](https://i.loli.net/2018/01/20/5a623b469e50a.png)

<!--more-->
# 将本地仓库与远程仓库关联
~~~
$ git remote add origin git@github.com:YotrolZ/helloTest.git
//git@开头的这个地址在github你刚刚建立的仓库里面找
~~~
![3.png](https://i.loli.net/2018/01/20/5a623c499ad73.png)
![4.png](https://i.loli.net/2018/01/20/5a623c4990a56.png)

# 拉取远程仓库信息
~~~
$ git pull origin master --allow-unrelated-histories 
~~~
这一步必须操作，否则是无法git push的，每次创建新的仓库关联这一步必须做得哦

然后就可以正常的使用git了，第一次push的时候需要写完整以下代码，以后就只需要git 
push就可以了。

~~~
$ git push -u origin master
~~~

# 具体步骤
1. git init
2. git remote add origin git@github.com:YotrolZ/helloTest.git
3. git pull origin master --allow-unrelated-histories 
4. touch a.html  【随意创建一个文件】
5. git add .
6. git commit -m add
7. git push -u origin master

**我也不知道为什么重复git add，但是就是这样才成功了**

