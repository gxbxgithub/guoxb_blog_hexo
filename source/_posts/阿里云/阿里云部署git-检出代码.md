---
title: 阿里云部署git-检出代码
categories: 阿里云
tags: 
- git
- 阿里云
---

用hexo在github上搭建了一个个人博客，本想写几篇文章被百度收录一下，过来很久去百度搜索，发现都没有被收录。

后来才知道，原来github上是拒绝被爬虫爬取的。那该怎么办？

<!--more-->

是不是把代码迁移出github就可以了？总之前前后后试了很多种办法，在这里为大家推荐三种方法：

* Coding代码托管

这种办法可行，而且是免费的，但是存在很大的不稳定性，因为访问网站很慢，后来在终端ping了一下，发现丢包很严重，果断放弃！

* 码云

很强大，部署上去访问速度也很快，如果不想花钱，那么域名只能是**.io；如果想自定义域名，需要花钱，不到100元搞定，而且可以试用一个月，很ok的，但是需要花钱，也放弃了！

* 阿里云服务器

因为本人有阿里云服务器，所以就开始研究如何将代码部署到阿里云服务器。因为hexo生成的是静态网页，尝试使用upload上传工具上传到服务器，配置nginx访问，非常完美！美中不足的是每次修改都需要用工具上传，太繁琐，于是就研究在阿里云部署git~~~

可以浏览下我的网站：[夜雨寒](http://guoxb.com/ '我的博客')

### 1. 安装git

本地终端通过`ssh manager@47.**.**.**`登录阿里云服务器，安装git

`$ yum install git`

### 2. 创建一个用户组-git

`$ groupadd git`

在用户组下创建一个用户，名字为git

`# adduser git -g git`

给git分配一个密码

`$ passwd git` 之后输入密码即可

### 3. 公钥

在服务器创建文件，配置公钥，用于git提交时免登陆

```
$ cd /home/git/
$ mkdir .ssh
$ chmod 700 .ssh
$ touch .ssh/authorized_keys
$ chmod 600 .ssh/authorized_keys
$ chown -R git:git .ssh
```

其中`/home/git`目录为服务器上用户git的主页目录，上述操作相当于在`/home/git/.ssh/`目录下新建一个`authorized_keys `文件。并把目录.ssh的权限设置为700，`authorized_keys `文件权限设置为600。

因为git的pull相当于读操作，push相当于写操作，所以需要读写权限。
复制客户端公钥到服务器`authorized_keys`文件

### 4. 服务器端初始化git仓库

```
$ cd /home/data
$ mkdir gitroot
$ chown git:git gitroot/
$ cd gitroot
$ git init --bare demo.git
$ chown -R git:git demo.git
```

现在可以在客户端克隆仓库

`git clone git@xxx.xxx.xxx.xxx:/home/data/gitroot/demo.git`

相信很多人和我一样，不仅仅是为了创建一个git仓库供大家使用就ok了；很多时候，我们需要上传的项目代码，例如：静态网页等，需要去使用。继续。。

### 5. 钩子设置-找到我们的代码

利用git的`hooks`中的`post-receive`来实现代码提交完成之后的动作。将仓库指定一个`--work-tree`然后进行检出操作`checkout --force`

在`/home/data/gitroot/demo.git`目录下，有一个hooks文件夹，编辑里面的`root-receive`文件，如果没有可以创建一个

```
cd /home/data/gitroot/demo.git/hooks
vi post-receive
```

在 post-receive 文件增加下面两行

```
# 指定我的代码检出目录
DIR=/home/demo
git --work-tree=${DIR} clean -fd
# 直接强制检出
git --work-tree=${DIR} checkout --force
```

这里需要注意一下：

`/home/demo` 目录需要手动去创建，并要给与较高的权限

```
cd /home
chmod 777 demo
```

最后，在客户端push我们的代码，就可以在服务器端 `/home/demo`目录下看到我们上传的代码了。

可以浏览下我的网站：[夜雨寒](http://guoxb.com/ '我的博客')