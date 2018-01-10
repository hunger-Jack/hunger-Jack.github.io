---
title: Hexo常见问题和配置
date: 2018-01-07 23:48:08
copyright: true
categories:
- web前端
tags:
- Hexo
- Next
---

### 异地管理博客

如果使用多台电脑发布更新博客，我自己的做法是在同一个远程仓库建立两个分支：
1. master分支，用户保存hexo里的public文件里面的内容，也就是hexo生成的博客文件。
2. hexo分支，用户保存所有的hexo的源文件【可能会暴露源文件secret key】

<!-- more -->
#### 基础假设
1. 已经搭建好hexo
2. 已经配置好next主题
3. 已经关联了github，可以使用github直接访问博客首页
4. 具体的简单操作没有截图，如果不会自行搜索即可找到答案



#### 创建本地git仓库
首先需要删除next下面的git配置，否则无法把next主题文件同步到远程仓库，进入到hexo根目录执行以下代码：
~~~
rm -fr ./themes/next/.git/
~~~
然后初始化hexo根目录
~~~
git init
~~~
创建一个.gitignore文件，放在hexo根目录，内容为：
~~~
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
~~~



#### 创建远程仓库的分支
目前远程仓库已经有一个默认的master分支，然后自己创建一个hexo分支，并且把hexo设置为默认分支，方便手动操作push本地源文件。
![](https://i.loli.net/2018/01/09/5a53a76ea4c8a.png)
![](https://i.loli.net/2018/01/09/5a53a8565d7f8.png)

#### push本地源文件到远程仓库hexo分支
进入到hexo根目录，使用终端打开一次执行以下代码：
~~~
git add .
git commit -m 'push to hexo'
git push origin master:hexo
~~~
这时候你可以看到远程仓库已经有本地的hexo源文件。以后每次发布博客之后就可以操作以上代码更新本地源文件到远程仓库的hexo分支了。
提醒：如操作过程中有其他报错小问题，需要自行google，这里只是说一个大概思路。

### next主题同步到远程仓库
我使用的是以下代码，可以同步到远程仓库hexo，但是具体原因不知道，就是成功了。上方提到的直接删除thems/next 下面的.git 文件我自己没有尝试，不知道有没有起作用
进入hexo根目录执行以下代码即可创建next主题：
~~~
git clone --recursive https://github.com/iissnan/hexo-theme-next themes/next
~~~
使用这种方式下载next主题就可以直接同步提交到远程仓库，但是后期更新的还不懂。