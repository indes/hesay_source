---
title: "🔧Kaldi初体验（三）：使用CVTE Mandarin Model进行语音识别"
date: 2018-08-03T20:07:37+08:00
draft: false
categories:
- 折腾
tags:
- ASR
- Kaldi
---

CVTE公司开源了一个训练好的模型，可以直接使用该模型进行识别。
<!--more-->
# 下载模型

下载地址：[http://kaldi-asr.org/models/m2](http://kaldi-asr.org/models/m2)

将下载的文件解压到 kaldi/egs 下。

# 运行模型

进入到 kaldi/src/online2bin 下，执行以下命令：

``` 
./online2-wav-nnet3-latgen-faster --do-endpointing=false --online=false --feature-type=fbank --fbank-config=../../egs/cvte/s5/conf/fbank.conf --max-active=7000 --beam=15.0 --lattice-beam=6.0 --acoustic-scale=1.0 --word-symbol-table=../../egs/cvte/s5/exp/chain/tdnn/graph/words.txt ../../egs/cvte/s5/exp/chain/tdnn/final.mdl ../../egs/cvte/s5/exp/chain/tdnn/graph/HCLG.fst 'ark:echo utter1 utter1|' 'scp:echo utter1 ../../egs/cvte/s5/data/wav/00030/2017_03_07_16.57.22_1175.wav|' ark:/dev/null
# 将../../egs/cvte/s5/data/wav/00030/2017_03_07_16.57.22_1175.wav替换成你的wav文件路径
```



# 效果预览

该模型对内存要求较高，在我本地16G的电脑上因为内存不足没能运行成功，最后在一台大内存的服务器上运行成功。并且预测时间也较长，一条九秒的语音，花费将近两分钟，有可能是因为没有GPU的缘故。

 <div align="center"> ![资源占用](https://o05g5zevc.qnssl.com/a7a02ff0-9384-40c6-9f9e-1fe63055b492/top.png)
预测时的资源占用</div>

 <div align="center"> ![运行结果](https://o05g5zevc.qnssl.com/534f61b7-938d-4c1c-86ac-8b0bbf23ed36/cvte.jpg)
识别结果对比（黑字为正确文本，白字为识别结果）</div>


可以看出，识别效果也不是很理想，如果想进一步提高性能，可以参考此文章：[cvte在线解码器源码修改（干货）](https://www.jianshu.com/p/5d4c39ca105a)

# References
- [Kaldi学习笔记（五）——使用CVTE训练好的SR模型做中文在线识别](https://blog.csdn.net/snowdroptulip/article/details/78952428)
- [研一刚接触语音识别，怎么运用kaldi工具箱做一个baseline？](https://www.zhihu.com/question/57230747/answer/244383490)