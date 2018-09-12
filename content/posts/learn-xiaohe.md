---
title: "⌨️我的双拼学习历程"
date: 2018-09-12T17:22:24+08:00
draft: false
categories:
    - 折腾

tags:
    - 双拼
---
学会双拼后，发现了新世界✨
<!--more-->
# 学习经历
本科的时候就想过要学习双拼，那时候还在键盘上贴过纸，可惜学了几天就没能坚持下去。
大约一个月前，又开始学习双拼。刚开始只是觉得想用空余时间学点不一样的东西，却没想到双拼能如此颠覆性的改变我的输入体验。

刚开始的时候的确很不适应，特别是使用手机的时候。因为自己之前是用九宫格的，换到全键盘后键位太小经常误触。前几天的时候，每天睡前使用双拼练习小程序学习十多分钟，慢慢记住键位，需要打字的时候也尽量使用双拼。大概两周的样子，就能脱离各种提示工具输入了。

学会双拼后，你就能体会到这种输入方案的优点了。虽然双拼并没有降低重码率，但是却大大降低了键盘敲击次数。前几天在Kindle上使用全拼输入法搜索文字的时候才发现自己竟然有点手足无措，现在已经完全不想再使用全拼了。

# 双拼方案选择
刚开始博主是准备选择微软双拼的，因为Windows 10和ios自带的输入法都支持。至于为什么后面选择了小鹤双拼，现在已经记不清原因了。


# 输入法选择
自己一直以来是原生输入法党，可惜ios自带输入法不支持小鹤双拼，无奈只能选择Google家的**Gboard**（需要去美区下载），颜值真的比国内那些第三方输入法高不少。
![](https://hesayhugo-1251211798.cosgz.myqcloud.com/IMG_1089.jpg)


刚开始的那几天，对键位还不是很熟悉，可以使用**讯飞输入法**，然后下载一个皮肤很快就能熟悉键位了。
![IMG_1087.PNG](https://upload-images.jianshu.io/upload_images/1014340-6c942fc0a2626515.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



Windows10 自带输入法不支持小鹤双拼，但是可以通过修改注册表的方式实现对小鹤双拼的支持：

1. 复制下示文本内容到文本编辑器，保存为后缀为.reg的文件
1. 直接双击文件导入注册表，确定提示框中的安全提示，你的Windows10微软输入法就默认为小鹤双拼了

```
Windows Registry Editor Version 5.00
[HKEY_CURRENT_USER\Software\Microsoft\InputMethod\Settings\CHS]
"LangBar Force On"=dword:00000000
"Enable Double Pinyin"=dword:00000001
"EmoticonTipTriggerCount"=dword:00000001
"HapLastDownloadTime"=hex(b):eb,69,29,59,00,00,00,00
"UserDefinedDoublePinyinScheme0"="FlyPY*2*^*iuvdjhcwfg xmlnpbksqszxkrltvyovt"
"DoublePinyinScheme"=dword:0000000a
"UDLLastUpdatedTime"="2017-05-27 22:01:40"
"UDLCount"=dword:0000018b
"UDLVisibleCount"=dword:0000018b
```
# 几个有用的小玩意
以下几个东西都是本人学习初期用到且十分有用的工具

- 小鹤方案壁纸（好像当初就是因为这个放弃的微软双拼）
![image](https://upload-images.jianshu.io/upload_images/1014340-2177f466ccd41c45.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 双拼练习小程序（刚开始的时候，睡前可以练十几分钟）
![image](https://upload-images.jianshu.io/upload_images/1014340-da85efd0ff80dce4?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 自己手抄的键位表（放在电脑旁边，十分有用）
