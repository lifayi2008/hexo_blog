---
title: 使用Hexo+github搭建博客
date: 2016-06-13 09:27:41
tags: [hexo,github]
categories: others
---

#### 安装nodejs环境

windows下下载安装包点击下一步直接安装即可，最新版本的nodejs安装好之后已经配置好环境变量可以直接使用node和npm命令
<!-- more -->
ubuntu下先安装最新版本的nodejs：

```bash    
$ sudo apt-get update
$ sudo apt-get install -y python-software-properties software-properties-common
$ sudo add-apt-repository ppa:chris-lea/node.js
$ sudo apt-get update
$ sudo apt-get install nodejs
```

然后安装npm(如果按照上面方法安装最新nodejs就已经有npm包了):

```bash
$ wget http://npmjs.org/install.sh
$ sudo chmod a+x install.sh
$ sudo ./install.sh
```

#### 安装hexo、创建博客根目录并初始化

```bash
$ npm install -g hexo-cli
$ mkdir hexo_bolg
$ cd hexo_blog
$ hexo init           //初始化博客根目录
$ npm install         //这条命令会根据你的package.json文件来安装相应的模块
```

安装一些额外的插件:

```bash
$ npm install hexo-deployer-git --save
$ npm install hexo-generator-feed@1 --save
$ npm install hexo-generator-sitemap@1 --save
$ npm install hexo-deployer-rsync --save
//npm install hexo-deployer-openshift --save
//npm install hexo-renderer-marked@0.2 --save
//npm install hexo-renderer-stylus@0.2 --save
//npm install hexo-deployer-heroku --save
```

#### 生成博客静态文件并启动内置web服务器进行预览

```bash
$ hexo generate       //如果未提示错误表示生成静态网站成功
$ hexo server
```

根据提示在浏览器中打开相应地址即可预览

#### 部署至github

要在github中创建一个库名为lifayi2008.github.com的仓库，然后将生成的静态页面和样式等文件提交到这个仓库即可使用http://lifayi2008.github.io来访问你的博客(lifayi2008为我的github账号名)

hexo为我们提供了推送文件比较方便的方法，编辑博客根目录中的_config.yml文件，找到最后的deploy配置块，改为(注意配置项冒号和后面的值中间有一个空格)：

    deploy:
      type: git
      repository: https://github.com/lifayi2008/lifayi2008.github.com.git
      branch: master

保存，然后运行*hexo deploy*

注意：如果不是在git bash中运行*hexo deploy*命令，你需要提前配置好本机git的邮箱和用户名

更方便的方法是使用github for windows，但是不翻墙根本下不下来好么

#### 绑定自己的域名

给你自己的域名添加一条CNAME记录到你的github的域名，比如我的lifayi2008.github.io

在本地博客根目录/source/目录下新建一个CNAME文件内容为你的域名，然后重新生成博客文件，并部署到github

#### 多地提交博客

因为hexo的git在提交博客代码前并不会从仓库拉取代码应用到本地，所以如果我们想从多台主机提交博客的话就需要将本地博客根目录也提交到一个安全的仓库，可以是你同名仓库的一个分支也可以是另外一个仓库，并且hexo已经帮我们创建一个.gitignore文件来帮我们确定必须要备份的文件

首先你应该创建另外一个仓库或者在lifayi2008.github.com中创建一个分支

然后在本地博客根目录中执行：

```bash
$ git init
$ git add *
$ git add .gitignore
$ git commit -m "all hexo blog file first commit"
$ git remote add origin your_repository_url
$ git push origin master
```

如果你要在另外一台电脑或者另外一个环境中继续提交你的博客的话可以执行下面的操作:

```bash
$ git clone your_repository_url hexo
$ cd hexo
$ npm install
    编辑博客
$ hexo generate
$ hexo deploy
```

#### Hexo的一些操作

新建文章或页面:

```bash   
$ hexo new post/page <title>        //title为文章标题或者页面标题
```

给文章添加标签或分类:

    ---
    title: 使用Hexo+github搭建博客
    date: 2016-06-13 09:27:41
    tags: [hexo,github]        //多个标签
    categories: others
    ---