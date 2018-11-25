---
title: "🛫如何搭建一个属于自己的 Telegram 推送 Bot"
date: 2018-11-25T11:16:28+08:00
draft: false
categories:
- 折腾
tags:
- Telegram
- telepush
- now.sh
---
首先，**标题一定要长！**

Telegram 实在是太好用了，个人最喜欢的还是全面且开放的 Bot api （还有比这更酷的im软件？），闲着无事写了一个类似于 [Server酱](http://sc.ftqq.com/) 的 Telegram 推送 Bot。
<!--more-->

GitHub: [telepush](https://github.com/indes/telepush)
# 为什么
已经有 Server酱 了，为什么还要重新写一个？

- Server酱 不开源，我不喜欢自己的消息从别人那里再走一遍
- Telegram 比微信好用


# 搭建方法
选择使用 PHP 编写就是考虑到需要方便搭建（虽然使用 Docker 不同语言的部署难度都一样），如果你有 vps 或者虚拟主机，那就按照一般的 PHP 程序那样部署就行了。

**这里我说一下如何使用 Docker 方式如何部署到 [Now.sh](https://now.sh) 上。**

## 安装 now-cli

Now.sh 是 ZEIT 提供的类似于类 Paas 服务平台，提供的免费套餐足够我们用来搭建一个 Telegram Bot 。

![](/img/your-own-telegram-push-bot/now_plan.png)

如果要将应用部署到 Now.sh 上，需要安装官方的 cli 工具。



### 使用 npm 安装
运行命令
```
npm i -g now
```
验证安装结果
```
now -v
```
如果有反应就说明安装成功了。


### 安装二进制文件

如果没有安装 node.js ，可以[下载二进制文件](https://github.com/zeit/now-cli/releases)，然后将文件加入到环境变量中。

如果遇到安装问题可以参考[官方文档](https://zeit.co/docs/v2/getting-started/installation/#now-cli)



### 登录 now-cli

```
now login
```

根据提示输入你的账户邮箱，然后 ZEIT 会给你发送一封验证邮件，点击邮件里面的 VERIFY 就可以了。



## 申请 Telegram Bot

详细流程可以参考 Telegram 的 [api 文档](https://core.telegram.org/bots#6-botfather) 申请 Bot。这里就大致说一下如果获取到我们需要Bot Token。

1. 关注 [BotFather](https://telegram.me/botfather)
2. 回复 */nowbot* ，根据他的回复分别输入想要申请的 Bot Name、Bot Username (结尾必须包含bot这三个字母)
3. 如果前面几步都没有问题，会回复你一串字符，这一串字符就是我们的 Bot Token，后面会用到。
4. 根据申请的 Bot Username ，关注刚申请的 Bot （如果不点击 START , Bot 是无法给你主动发送消息的）。



### 获取你的Telegram id

关注 [What's my Telegram ID?](https://t.me/my_id_bot) ，这个机器人告诉你当前 Telegram 账户的 id ，这个 id 也是我们后面需要用到的。



## 部署 telepush

Now.sh 最近增加了对 PHP 的支持，但是还是不太完善，支持的功能有限。我们还是使用 Docker 方式部署 telepush。



下载代码

```shell
git clone https://github.com/indes/telepush && cd telepush
```

修改 .env.example 

```
BOT_TOKEN=<上面申请的Bot Token>
OWNER_ID=<你的Telegram ID>
PROXY=<本地调试使用，不用管>
```

部署

```
now
```

运行上面这个命令后，now-cli 会自动推送代码到 Now 上并且构建 Docker 镜像，输出中有一串**蓝色**的网址，这就是我们的 Bot 地址。

浏览器访问 

```
<Bot 地址>/ping
``` 

如果 Bot 给你发送了一条 ping ，这就说明 Bot 部署成功了。

# Bot 使用

```
GET <Bot 地址>/api/send?msg=xxx 
```

其中xxx为你要发送消息的内容。

telepush 目前还只具有最基本的文本推送功能，其他功能还待进一步开发。