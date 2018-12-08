---
title: "☁️通过 Now.sh 免费部署 Disqus 代理"
date: 2018-12-08T21:18:49+08:00
draft: false
categories:
- Code
tags:
- Now.sh
- Disqus
- Docker
---

一直使用 Disqus 作为评论系统，众所周知的原因，Disqus 无法在国内正常访问。博主有一台位于海外的 VPS 使用搭建代理，但是有些没有海外服务器的朋友碰到这种情况就无能为力了。  
其实通过 Now.sh 平台提供的 Docker 部署服务，不需要自己有海外服务器也能部署自己的 Disqus 代理服务。

<!--more-->
使用的代理方案是 [@fooleap](https://blog.fooleap.org) 写的 [disqus-php-api](https://github.com/fooleap/disqus-php-api)。

## Disqus 设置

这一步参考[项目README](https://github.com/fooleap/disqus-php-api#disqus-%E8%AE%BE%E7%BD%AE)

其中 Callback URL 先随便填一个 URL，后面需要修改。
![](/img/deploy-disqus-proxy-by-now/3436.png)

## 部署
- 安装 Now CLI

首先请注册一个 [Now.sh](https://zeit.co/now) 账户。

参考我的这篇文章 [📟如何搭建一个属于自己的 Telegram 推送 Bot](/posts/your-own-telegram-push-service/#%E5%AE%89%E8%A3%85-now-cli) 或者官方文档 [Installation - ZEIT Documentation](https://zeit.co/docs/v2/getting-started/installation/#from-npm) 安装 Now CLI。

- 下载代码

我已经编写好相应的 Dockerfile 和 Caddyfile，直接下载我 fork 的仓库就行。
```shell
git clone https://github.com/indes/disqus-php-api && cd disqus-php-api
```

- 修改配置

参照 [项目README](https://github.com/fooleap/disqus-php-api#disqus-%E8%AE%BE%E7%BD%AE) 填写修改 api/config.php 文件。

- 部署

登录 Now Cli，输入以下命令，并且根据提示输入账户邮箱。
```
now login
```

部署 Docker
```
now
```
![](/img/deploy-disqus-proxy-by-now/35.png)

部署过程中会询问你是采用哪种方式部署，键盘输入 Docker 前那个数字。

图中蓝色的链接就是我们部署的 Docker 服务地址，复制保存下来，后文中的 `<url>` 都指这个链接。如果有需要可以参考官方文档 [Aliasing A Deployment - ZEIT Documentation](https://zeit.co/docs/v2/domains-and-aliases/aliasing-a-deployment/) 创建别名，这里就不再赘述。

浏览器访问 `<url>/api/login.php` `<url>`为前面保存的蓝色链接，如果能看到一个动画说明部署成功。

- 修改 Disqus 配置

打开 [API - Disqus](https://disqus.com/api/applications/)，找到刚申请的 Application，修改 Settings 中的 Callback URL 为`<url>/api/login.php`

## 嵌入评论框

### 引入 CSS

```html
<link rel="stylesheet" href="<url>/dist/iDisqus.min.css" />
```

### 创建容器

```html
<div id="comment"></div>
```

### 引入 JS

```html
<script src="<url>/dist/iDisqus.min.js"></script>
```

### 创建实例

```javascript
var disq = new iDisqus('comment', {
    forum: '',
    api: '<url>/dist/api',
    site: 'Your blog url',
    mode: 1,
    timeout: 3000,
    init: true
});
```

然后你就能去你的博客看成果了。