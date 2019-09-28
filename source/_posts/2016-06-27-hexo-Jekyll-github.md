---
title: hexo/Jekyll + github搭建自己的博客
date: 2016-06-27 15:41:41
tags:
  - hexo
  - Jekyll
  - GitHub
---
首先，必须有一个GitHub账号，相信大家都有，没有的话自己去创建吧。

### 一、GitHub Pages的使用

#### 1. 创建一个分支，使用你的GitHub name

Head over to GitHub and create a new repository named username.github.io, where username is your username (or organization name) on GitHub.

我的GitHub name是 SkylerHu，以我的名字为例。

#### 2. 初始化项目

**若是借助Jekyll和hexo的话，只需clone下来即可。**

> 已下步骤是基于terminal的git命令，若是使用界面工具，自己去[GitHub Pages](https://pages.github.com/)看

``` bash
~$ git clone https://github.com/SkylerHu/SkylerHu.github.io
~$ cd SkylerHu.github.io
~$ echo "Hello World" > index.html
~$ git add --all
~$ git commit -m "Initial commit"
~$ git push -u origin master
```
也可以在index.html中添加HTML内容，然后在push

一切OK之后，可以访问 http://SkylerHu.github.io.  可以查看index.html的内容。这个网址就是你的博客网址。

### 二、使用Jekyll

上面新建的项目中，Settings中可以看到GitHubPages相关配置，官方推荐使用[Jekyll](http://jekyll.bootcss.com/)
![GitHub Pages Settings](http://blog-img.skylerhu.com/2016/06/27/81479b8884.png)

#### 1. 初始化

安装需要Ruby环境，没有的话自行安装。

``` bash
~$ gem install jekyll
~$ jekyll new my-awesome-site
```

把项目生成的所有文件，全部copy到`SkylerHu.github.io`目录下.

#### 2. 目录结构

* [目录结构](http://jekyll.bootcss.com/docs/structure/)

#### 3. 本地启动预览

``` bash
~$ cd SkylerHu.github.io
~/SkylerHu.github.io$ jekyll serve
```
直接访问http://localhost:4000/  就可以看到你的网站了。

#### 4. 写文章

在目录`/SkylerHu.github.io/_posts/`下新建xxxx.md；
格式需要按照模板提供的去写； 

#### 5. 部署

``` bash
~/SkylerHu.github.io$ git add --all
~/SkylerHu.github.io$ git push
```

#### 6. 使用自己的域名

##### 1) 添加CNAME文件push到GitHub
``` bash
~/SkylerHu.github.io$ echo "pages.skylerhu.com" > CNAME
```
##### 2) 域名解析
使pages.skylerhu.com解析到 SkylerHu.github.io
注意选择 解析类型是`CNAME`

#### 7. 主题

推荐网站：
* <http://jekyllthemes.org/>
* <https://jekyllthemes.io/>

可以根据自己的喜好去选择主题，都大同小异。

### 三、使用hexo

之所有使用[hexo](https://hexo.io/zh-cn/),因为好多网友都说这个比较好，所有研究了一下使用方法。

需要安装[Node.js](https://nodejs.org/en/)

#### 1. 安装

``` bash
~$ sudo npm install -g hexo
```

#### 2. 初始化

``` bash
~$ cd blog
# 初始化
~/blog$ hexo init
# 生成静态页面，把markdown转化成html，hexo g 也行
~/blog$ hexo generate
# 启动本地服务，hexo s 也行
~/blog$ hexo server
```
其他[hexo命令](https://hexo.io/zh-cn/docs/commands.html)

若是之后nodejs升级了，需要执行一下 `npm --registry=https://registry.npm.taobao.org install`
或者直接执行 `npm install`, 有参数的意思是使用淘宝源

#### 3. 配置GitHub

编辑blog目录下_config.yml文件，详见[hexo配置](https://hexo.io/zh-cn/docs/configuration.html)

``` bash
~/blog$ vim _config.yml
```

找到`deploy`配置如下：
``` config
deploy:
     type: git
     repo:https://github.com/SkylerHu/SkylerHu.github.io.git
     branch:master
     message: commit的message，默认 Site updated: {{ now('YYYY-MM-DD HH:mm:ss') }}
```

保存后执行命令安装插件[hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)
``` bash
~/blog$ npm install hexo-deployer-git --save
```

#### 4. 配置RSS

编辑_config.yml配置feed
``` config
feed:
  type: atom
  path: atom.xml
  limit: 20
  hub:
```
安装插件[hexo-generator-feed](https://github.com/hexojs/hexo-generator-feed)

``` bash
~/blog$ npm install hexo-generator-feed --save
```

#### 5. 部署GitHub Pages

部署之前可以修改_confi.yml中deploy.message作为git commit的message
``` bash
# 每次修改之后都需要clean和generate
~/blog$ hexo clean
~/blog$ hexo generate
# hexo d 也可以
~/blog$ hexo deploy
```

会生成一个`.deploy_git` 文件夹同步到GitHub项目中。

#### 5. 主题

推荐网站<https://hexo.io/themes/>
我自己使用的是 [hexo-theme-yilia](https://github.com/litten/hexo-theme-yilia)

``` bash
~$ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```
修改hexo根目录下的 _config.yml ： theme: yilia

#### 6. 使用自己的域名

同Jekyll一样，也需要修改域名解析，同样也要在GitHub项目下添加CNAME文件。

> 注意：
> *  不能直接在`deploy_git`目录下直接添加CNAME文件，每次generate之后会删除
> * 需要添加到`source`文件夹下`echo "pages.skylerhu.com" > source/CNAME`

#### 7. 其他配置

可以在blog/_config.yml配置[语言和时区](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)
``` config
language: zh-Hans
timezone: Asia/Shanghai
```

我的网站还添加了`多说评论`和`百度统计`，具体使用详见[推荐网站](http://ijiaober.github.io/categories/hexo/)。

### 四、比较

| | Jekyll | hexo |
| ---- | ---- | ---- |
| 语言 | Ruby | node.js |
| 效率 | So fast | 稍慢 |
| 部署 | 需要多个命令 | 直接使用git命令 |

> 两个之间都可以互相迁移，看个人操作习惯和爱好吧。

### 遇到的问题

升级了Node.js后，发现在项目目录中执行hexo相关命令会报出错误

``` log
[Error: Module version mismatch. Expected 47, got 46.]
{ [Error: Cannot find module './build/default/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
{ [Error: Cannot find module './build/Debug/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
```
解决方案如下：
* 执行npm uninstall hexo卸载hexo；
* 重新安装hexo，npm install -g hexo；
* 到博客目录执行npm install，hexo clean，hexo generate
