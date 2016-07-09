layout: post
title: "使用Gitlab的内置nginx去服务其他静态内容"
date: 2015-05-19 13:05:00
tags:
- Gitlab
- Nginx
categories:
- 技术
---

有时候，我们使用gitlab官方提供的deb去安装时，gitlab使用了内嵌的nginx服务，使用
chef去管理nginx的配置文件，所以如果我们在gitlab的服务器上想要使用其他的nginx服务
就很不方便，gitlab官方提供了[方案](https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/doc/settings/nginx.md)是：

- 停用内嵌的nginx，而使用外部的nginx，然后在外部的nginx安装文件里装入gitlab的配置

但是如果我们不想再安装外部的nginx，而直接使用内嵌的nginx，可以使用如下方案：

- [修改](http://stackoverflow.com/questions/24090624/using-gitlabs-nginx-to-serve-another-app)nginx的chef recipes

这个方法很好用，当然我们也可以直接修改：

- 在原来的nginx配置文件(` /opt/gitlab/embedded/cookbooks/gitlab/recipes/nginx.rb
`)中加入自定义的内容
