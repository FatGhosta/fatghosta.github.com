---
layout: post
title: "在CentOS中升级gitlab，从6.0升级到6.5"
date: 2014-02-08 11:26:00 +0800
comments: true
categories: 
---
春节期间，趁着同事们都不写代码的时候，我对gitlab进行了一次升级，版本从6.0升级到6.5。  
其实6.0到6.5增加的新特性不是很多，但是我怕以后升级不好升了，就抓紧春节这个机会做了升级。

首先，看[官方文档](https://github.com/gitlabhq/gitlabhq/blob/master/doc/update/6.0-to-6.5.md)。 
其实这事儿特别巧，当我决定春节升级的时候，gitlab的版本还在6.4，我知道每月20多号会有个新版本。
但是令我愉快地是，居然官方给提供了一个6.0到6.5的升级文档，要不然我还得一个版本一个版本地升级（保险起见）。

下面我记录一下，在按照官方版本升级的过程中我遇到的问题：  
1. 我第一次安装gitlab的时候特别巧，死活git clone不下gitlab。所以我是在github.com下载的zip包进行的解压。所以这次升级，我无法git fetch了。我是怎么升级的呢？首先备份了一下数据库，然后mv原来的gitlab文件夹到别处，最后克隆gitlab项目。  
2. 依旧是gitlab在`bundle install`过程中的老问题，charlock_holmes！源gem是依赖charlock_holmes的0.6.9.4版本，安装0.6.9，然后在gitlab文件夹下执行一次`bundle`,就可以了。或者按照这个方法[How to fix CharlockHolmes](https://github.com/gitlabhq/gitlabhq/issues/679#wiki-issuecomment-5282141)。
3. 安装logrotate。在CentOS里肯定不能apt-get了，`yum install logrotate*`。
4. 整个过程很顺利，但是在完成升级后测试。遇到了`git clone http://mygitlab.com/me/project.git`出错的问题，具体问题见下：[issue5774](https://github.com/gitlabhq/gitlabhq/issues/5774)。解决办法是升级nginx版本到1.4.4。

下面我附一下nginx的升级过程，从0.8到1.4.4：[官方文档](http://wiki.nginx.org/Install#Official_Red_Hat.2FCentOS_packages)
1. 创建/etc/yum.repos.d/nginx.repo文件
2. 编辑文件内容如下:
```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/5/x86_64
gpgcheck=0
enabled=1
```  
3. 执行`yum install nginx`即可完成升级

注：在官方文档中提及第二步中添加的baseurl似乎不对，于是我在这个网址(http://nginx.org/packages/centos/) 下找到合适我服务器的baseurl。

