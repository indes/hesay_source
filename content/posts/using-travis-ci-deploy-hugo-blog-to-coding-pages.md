---
title: "🚀使用Travis CI自动部署Hugo博客到Coding Pages"
date: 2018-08-17T19:50:11+08:00
draft: false
categories:
- 折腾
tags:
- Hugo
- Travis CI
- 持续集成

---

因为朋友的安利和Coding又送了我三个月的黄金会员，将博客从[Netlify](https://www.netlify.com/ "Netlify: All-in-one platform for automating modern web projects.")迁到了[Coding Pages](https://coding.net/pages/ "Coding Pages | 静态页面托管服务")。Coding Pages的服务器位于香港，比使用Tokyo aws的Netlify要快不少。但是习惯了曾经直接将博客源码推送到Git仓库，GitLab CI/CD或Netlif自动构建部署的方式，在Coding Pages上还得push一遍public文件夹让我觉得很繁琐。于是研究了一下GitHub上使用的比较多的Travis CI。

<!--more-->

# 前提条件

本文默认你已经具备一下技能：

- 使用过Hugo或其它的静态博客
- 博客源码托管在GitHub仓库
- 知道怎么使用GitHub Pages或者Coding Pages服务

# 准备工作
使用GitHub账号登录[Travis CI](https://travis-ci.org/)，然后选择你的博客源码仓库，将右边的按钮打开。
![](https://hesay-me-1251211798.file.myqcloud.com/img/using-travis-ci-deploy-hugo-blog-to-coding-pages/travis-ci.png)


# 生成token
打开[Coding token管理页面](https://coding.net/user/account/setting/tokens)，新建令牌，将project:depot权限勾上。然后就会生成一个token，改token只会显示一次，所以先别关闭页面。
![](https://hesay-me-1251211798.file.myqcloud.com/img/using-travis-ci-deploy-hugo-blog-to-coding-pages/coding-token.png)


打开Travis CI项目的设置页面
![](https://hesay-me-1251211798.file.myqcloud.com/img/using-travis-ci-deploy-hugo-blog-to-coding-pages/travis-ci1.png)


新建名为CO_TOKEN的环境变量，将刚生成的codng token填入。
![](https://hesay-me-1251211798.file.myqcloud.com/img/using-travis-ci-deploy-hugo-blog-to-coding-pages/token-setting.png)

# Travis CI配置
在仓库的根目录创建 .travis.yml 文件，将一下内容复制到文件中，并根据注释修改。
```
language: go

install: go get -v github.com/gohugoio/hugo

script:
    - hugo
    
deploy:
    - cd ./public
    - git config user.name "username" # username替换为你的git username
    - git config user.email "email" # email替换为你的coding邮箱
    - git init
    - git add .
    - git commit -m "Travis-CI 自动部署"
    # Coding Pages
    - git push --force "https://<username>:${CO_TOKEN}@${CO_REF}" master:master # username替换为你的coding用户名

env:
    global:
        # Coding Pages
        - CO_REF: # 这里填入你的coding pages仓库地址 例如：git.coding.net/username/hugo.git    
```
然后push代码就行了。

# References
- [使用 Hugo 搭建博客](https://segmentfault.com/a/1190000012975914)
- [Hexo遇上Travis-CI：可能是最通俗易懂的自动发布博客图文教程](https://juejin.im/post/5a1fa30c6fb9a045263b5d2a)