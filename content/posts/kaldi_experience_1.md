---
title: "🎤Kaldi初体验（一）：安装"
date: 2018-07-15T14:39:05+08:00
draft: false
categories:
- 折腾
tags:
- ASR
- Kaldi
---

最近对Kaldi这个工具箱有了一些粗浅的体验，踩了不少坑。于是就稍微整理了一下。

<!--more-->
# 下载代码

Kaldi托管在GitHub上，首先需要将代码下载到本地。

```shell
git clone https://github.com/kaldi-asr/kaldi
```

然后进入到Kaldi文件夹，可以观察以下Kaldi的目录结构。

其中./tools，./src和./egs三个目录比较重要：

- ./tools 目录存放Kaldi依赖的包
- ./src 目录存放Kaldi的源代码
- ./egs 目录存放Kaldi官方提供的一些例子

# 编译安装

## 检查依赖环境

运行以下命令：

```shell
cd tools
extras/check_dependencies.sh
```

一般会提示需要安装一些依赖的环境，根据提示安装就行。安装完依赖后再运行一次 `extras/check_dependencies.sh` 出现以下结果就行了。

![](https://hesay-me-1251211798.file.myqcloud.com/img/kaldi_experience_1/check_dependecies.png)

## 编译

```shell
make -j 20 # 20为并行处理的作业数，可以根据自己的cpu核心数进行修改
```

然后程序就会自己从网上下载需要的文件进行编译了

![](https://hesay-me-1251211798.file.myqcloud.com/img/kaldi_experience_1/make_tools.png)



然后切换到../src目录运行以下命令：

```shell
make depend -j 20
make -j 20 # 这一步花费的时间比较长
```

![](https://hesay-me-1251211798.file.myqcloud.com/img/kaldi_experience_1/make.png)

kaldi就安装完成了，过程还算比较简单。


## 验证安装
安装完成了，下面我们验证以下是否安装成功。
进入 `kaldi/egs/yesno/s5` 目录，运行 `./run.sh`。
脚本首先会下载数据，然后等待运行结果就行了。
![](https://hesay-me-1251211798.file.myqcloud.com/img/kaldi_experience_1/yesno.png)

# References
- [Kaldi tutorial: Getting started](http://kaldi-asr.org/doc/tutorial_setup.html)
- [Kaldi学习手记（一）：Kaldi的编译安装](https://blog.csdn.net/by21010/article/details/49072699)
- [有趣的开源软件：语音识别工具Kaldi (一)](https://blog.csdn.net/amds123/article/details/70313780)
- [kaldi部署](https://zhuanlan.zhihu.com/p/26464775)