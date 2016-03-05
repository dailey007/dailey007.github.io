---
layout: post
title:  "hello jekyll!"
comments: true
---

##jekyll是什么？
到[这里](http://jekyll.bootcss.com/)来看吧，简单来说，就是将纯文本转化成静态网页，可以在github上布置使用。

##环境搭建
在Ubuntu 或 linux mint 上搭建jekyll 本地环境，比较麻烦，主要是要使用ruby2.0以上版本，而目前在官方源上，ruby版本还停留在1.9.0...

#### 安装ruby2.0版本
网上说安装方法有三种，第一种不靠谱，使用第二种方式安装成功。
方法二：使用brightbox ppa仓库安装

	sudo apt-get install python-software-properties
	sudo apt-add-repository ppa:brightbox/ruby-ng
	sudo apt-get update
	sudo apt-get install ruby2.1 ruby2.1-dev

#### 安装jekyll

在Terminal中执行 sudo gem install jekyll, 没有问题的话，就可以安装成功。

##jekyll使用

下面这篇文章写的很好，仅从里面摘录部分列举如下
https://segmentfault.com/a/1190000000406013

#### 创建blog 的文件夹

```
	jekyll new blog
```

这样就会创建一个新文件夹blog，其结构如下：

+ 文件夹_layouts：用于存放模板的文件夹，里面有两个模板，default.html和post.html
+ 文件夹_posts：用于存放博客文章的文件夹，里面有一篇markdown格式的文章--2014-01-27-welcome-to-jekyll.markdown
+ 文件夹css：存放博客所用css的文件夹
+ gitignore：可以删掉，后面会将项目添加到git项目，所以这个不需要了
+ _coinfig.yml：jekyll的配置文件，里面可以定义相当多的配置参数，具体配置参数可以参照其官网
+ index.html：项目的首页

根据实际需要，可能还需要创建如下文件或文件夹：
_includes:用于存放一些固定的HTML代码段，文件为.html格式，可以在模板中通过liquid标签引入，常用来在各个模板中复用如导航条、标签栏、侧边栏之类的在每个页面上都一样不变的内容，需要注意的是，这个代码段也可以是未被编译的，也就是说也可以使用liquid标签放在这些代码段中image和js等自定义文件夹：用来存放一些需要的资源文件，如图片或者javascript文件，可以任意命名CNAME文件：用来在github上做域名绑定的，将在后面介绍favicon.ico：网站的小图标等

#### 编写文章

大致上jekyll生成html的流程，jekyll首先会读取如下内容进入内存中：
+ _posts及文件夹下的所有文章，将其参数和文章内容组织保存在内存中，所有的文章的内容、参数都在site.posts对象（其他文件夹下的文章不会放入site.posts中）
+ _layouts文件夹下的所有模板
+ _includes文件夹下的所有需要被引入的内容
然后根据每一篇需要编译的文章选择的其参数定义的模板来创建一个模板，并将当前文章的内容、参数等进行扩展后放在page对象、content对象中，然后进行模板的编译，生成html文件，并按照一定规则放在_site文件夹下。也就是说在创建一篇文章时，其实所有文章的内容都已经被读取出来了，这也为文章相互之间的关联提供了可

#### 调试

```
		jekyll build --trace
```

在博客文件夹下，在命令行中输入jekyll build --trace就可以将所有文章文件根据其模板进行编译，生成结果，放在根目录下的_site中.

#### 本地查看

```
		jekyll serve
```

这样会开启一个监听端口4000的服务器，浏览器中查看http://localhost:4000，则会进入index.html的内容中，点击文章的标题就可以跳转到具体的博文了。
