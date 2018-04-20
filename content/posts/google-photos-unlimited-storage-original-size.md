---
title: "非Pixel开启Google Photos原画质无限容量"
date: 2018-04-20T16:28:21+08:00
draft: false
categories:
- 折腾
tags:
- Google Photo
- Google Lens
---

G家的良心应用还是很多的，Google Photos就是一个很好用的应用。无限容量的照片存储很好用，就是非Nexus和Pixel设备会对照片进行压缩（虽然我真没看出来压缩后的变化），谷歌亲儿子则没有这个限制并且还有Google Lens这个有意思的工具。

<!--more-->

## 前提条件

手机必须root，安装GApps并登录谷歌账号

## 步骤

1. 下载[nexus.xml](http://7b1ft4.com1.z0.glb.clouddn.com/nexus.xml?attname=)
2. 复制nexus.xml文件到手机/system/etc/sysconfig目录下
3. 更改此文件权限为644，就是rw-r–r–
![](https://ws1.sinaimg.cn/orj360/0067saHNgy1fqj8minmjoj30u01hcad6.jpg)

4. 重启手机

## 验证
打开Google Phones，在 设置/备份与同步/备份账号 的下面会多处一句话：
![](https://ws1.sinaimg.cn/mw690/0067saHNgy1fqj8ttzgdbj30tz0eudh8.jpg)

> 您可以获得无限免费存储空间，以原画质上传Pixel设备中的照片和视频

如果出现这句话，那么就说明成功了。

我对于原画质无限容量的需求倒是不大，反倒是觉得集成的Google Lens挺有意思的。
![](https://ws1.sinaimg.cn/large/0067saHNgy1fqjax9mr0kj31m91d14qp.jpg)

找到一张照片，然后点击那个按钮就能识别了，对物体识别率还不错。
![](https://ws1.sinaimg.cn/large/0067saHNgy1fqjb0knh3bj31ob1hchdt.jpg)
![](https://ws1.sinaimg.cn/large/0067saHNgy1fqjbbd3013j31ob1hc7wh.jpg)

## References
- [How to Enable Google Photos Unlimited Storage and Pixel-Exclusive Wallpapers on Any Phone](https://www.xda-developers.com/enable-google-photos-unlimited-storage/)
- [解开封印：Google Photos不限容量原画质上传](https://otichi.com/google-photos-unlimited-storage-original-size.html)