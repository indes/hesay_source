---
title: "Kaldi初体验（二）-  thchs30运行"
date: 2018-07-30T21:17:30+08:00
draft: false
categories:
- 折腾
tags:
- ASR
- Kaldi
---

上篇文章介绍了Kaldi工具箱的安装，本篇文章介绍一下Kaldi自带的中文语音识别例程thchs30的运行。

<!--more-->

# 下载数据

首先，我们需要准备训练数据。到这个网站下载数据 [http://www.openslr.org/18/](http://www.openslr.org/18/)

一共有三个文件需要下载：

![下载数据](https://o05g5zevc.qnssl.com/57a2a256-7266-4e6a-a238-299bb7c6ebfe/download_data.png)

下载完成后，将数据解压到 kaldi/egs/thchs30/s5/ 下的```thchs30-openslr```文件夹中（如果不存在则手动创建该文件夹）。



# 修改运行脚本

如果要在本地运行这个例子，需要对运行脚本做一些修改。

1. 修改 /thchs30/s5/cmd.sh 

   将/thchs30/s5/cmd.sh文件中的queue.pl替换为run.pl：

   ```shell
   export train_cmd=run.pl
   export decode_cmd=run.pl
   export mkgraph_cmd=run.pl
   export cuda_cmd=run.pl
   ```

2. 修改 /egs/thchs30/s5/run.sh

   将第11行的thchs=修改为之前下载的数据的解压路径：

   ```shell
   thchs=./thchs30-openslr
   ```

   将第8行，n=修改为并行任务数(一般以cpu核心数为标准，比如我的i5是6核无超线程，于是就设置为6)：

   ```shell
   n=6
   ```



# 训练模型

执行```./run.sh```就可以开始训练模型了。

这一步持续的时间会比较长，我在i5-8400、16G，Ubuntu 18.04下，用了一晚上的时间。由于没有GPU，所以到脚本到了训练DNN这一步就会停止了。

# 使用训练的模型进行语音识别

模型训练完了，接下就是验证模型效果了。



## 安装portaudio

1. 切换到 kaldi/tools/ 目录下，运行 ```./install_portaudio.sh```
2. 切换到 kaldi/src 下，运行 ```make ext```



## 创建文件

1. 将 kaldi/egs/voxforge/ 下的 online_demo 文件夹复制到 kaldi/egs/thchs30/ 下
2. 进入 kaldi/egs/thchs30/online_demo ，新建 online-data 和 work 两个文件夹，在 online-data 下建立audio 和 models 两个文件夹
3. 将要识别的wav文件复制到 audio 下，这里我们将 kaldi/egs/thchs30/s5/thchs30-openslr/data_thchs30/train/ 下的 A2_0.wav、A2_1.wav、A2_2.wav、A2_3.wav 这几个文件复制过来。需要注意的是，这几个文件同时是模型的训练数据，一般来说不能将训练数据用了做测试数据。不过我们这里也就简单演示以下运行流程，真实场景下是不会使用这些数据的
4. 在 models 目录下新建 tri1 文件夹，将 kaldi/egs/thchs30/s5/exp/tri1/ 下的 final.mdl和35.mdl 复制过来，以及 kaldi/egs/thchs30/s5/exp/tri1/graph_word/ 下的 words.txt 和 HCLG.fst 也复制过来。



## 修改脚本

对 kaldi/egs/thchs30/online_demo/run.sh 做如下修改：

1. 将31-39行的代码注释，即

   ```shell
   if [ ! -s ${data_file}.tar.bz2 ]; then
      echo "Downloading test models and data ..."
      wget -T 10 -t 3 $data_url;
   
      if [ ! -s ${data_file}.tar.bz2 ]; then
          echo "Download of $data_file has failed!"
          exit 1
      fi
   fi
   ```

   改成

   ```shell
   #if [ ! -s ${data_file}.tar.bz2 ]; then
   #    echo "Downloading test models and data ..."
   #    wget -T 10 -t 3 $data_url;
   #
   #    if [ ! -s ${data_file}.tar.bz2 ]; then
   #        echo "Download of $data_file has failed!"
   #        exit 1
   #    fi
   #fi
   ```

2. 找到

   ``` 
   ac_model_type=tri2b_mmi
   ```

   并将其修改为

   ```
   ac_model_type=tri1
   ```

3. 找到

   ```
   online-wav-gmm-decode-faster --verbose=1 --rt-min=0.8 --rt-max=0.85\
               --max-active=4000 --beam=12.0 --acoustic-scale=0.0769 \
               scp:$decode_dir/input.scp $ac_model/model $ac_model/HCLG.fst \
               $ac_model/words.txt '1:2:3:4:5' ark,t:$decode_dir/trans.txt \
               ark,t:$decode_dir/ali.txt $trans_matrix;;
   ```

   修改为

   ```
   online-wav-gmm-decode-faster --verbose=1 --rt-min=0.8 --rt-max=0.85\
               --max-active=4000 --beam=12.0 --acoustic-scale=0.0769 \
               scp:$decode_dir/input.scp $ac_model/final.mdl $ac_model/HCLG.fst \
               $ac_model/words.txt '1:2:3:4:5' ark,t:$decode_dir/trans.txt \
               ark,t:$decode_dir/ali.txt $trans_matrix;;
   ```


最终的脚本为：

```shell
#!/bin/bash

# Copyright 2012 Vassil Panayotov
# Apache 2.0

# Note: you have to do 'make ext' in ../../../src/ before running this.

# Set the paths to the binaries and scripts needed
KALDI_ROOT=`pwd`/../../..
export PATH=$PWD/../s5/utils/:$KALDI_ROOT/src/onlinebin:$KALDI_ROOT/src/bin:$PATH

data_file="online_data"
data_url="http://sourceforge.net/projects/kaldi/files/online-data.tar.bz2"

# Change this to "tri2a" if you like to test using a ML-trained model
    ac_model_type=tri1

# Alignments and decoding results are saved in this directory(simulated decoding only)
decode_dir="./work"

# Change this to "live" either here or using command line switch like:
# --test-mode live
test_mode="simulated"

. parse_options.sh

ac_model=${data_file}/models/$ac_model_type
trans_matrix=""
audio=${data_file}/audio

#if [ ! -s ${data_file}.tar.bz2 ]; then
#    echo "Downloading test models and data ..."
#    wget -T 10 -t 3 $data_url;
#
#    if [ ! -s ${data_file}.tar.bz2 ]; then
#        echo "Download of $data_file has failed!"
#        exit 1
#    fi
#fi

if [ ! -d $ac_model ]; then
    echo "Extracting the models and data ..."
    tar xf ${data_file}.tar.bz2
fi

if [ -s $ac_model/matrix ]; then
    trans_matrix=$ac_model/matrix
fi

case $test_mode in
    live)
        echo
        echo -e "  LIVE DEMO MODE - you can use a microphone and say something\n"
        echo "  The (bigram) language model used to build the decoding graph was"
        echo "  estimated on an audio book's text. The text in question is"
        echo "  \"King Solomon's Mines\" (http://www.gutenberg.org/ebooks/2166)."
        echo "  You may want to read some sentences from this book first ..."
        echo
        online-gmm-decode-faster --rt-min=0.5 --rt-max=0.7 --max-active=4000 \
           --beam=12.0 --acoustic-scale=0.0769 $ac_model/final.mdl $ac_model/HCLG.fst \
           $ac_model/words.txt '1:2:3:4:5' $trans_matrix;;

    simulated)
        echo
        echo -e "  SIMULATED ONLINE DECODING - pre-recorded audio is used\n"
        echo "  The (bigram) language model used to build the decoding graph was"
        echo "  estimated on an audio book's text. The text in question is"
        echo "  \"King Solomon's Mines\" (http://www.gutenberg.org/ebooks/2166)."
        echo "  The audio chunks to be decoded were taken from the audio book read"
        echo "  by John Nicholson(http://librivox.org/king-solomons-mines-by-haggard/)"
        echo
        echo "  NOTE: Using utterances from the book, on which the LM was estimated"
        echo "        is considered to be \"cheating\" and we are doing this only for"
        echo "        the purposes of the demo."
        echo
        echo "  You can type \"./run.sh --test-mode live\" to try it using your"
        echo "  own voice!"
        echo
        mkdir -p $decode_dir
        # make an input .scp file
        > $decode_dir/input.scp
        for f in $audio/*.wav; do
            bf=`basename $f`
            bf=${bf%.wav}
            echo $bf $f >> $decode_dir/input.scp
        done
        online-wav-gmm-decode-faster --verbose=1 --rt-min=0.8 --rt-max=0.85\
            --max-active=4000 --beam=12.0 --acoustic-scale=0.0769 \
            scp:$decode_dir/input.scp $ac_model/final.mdl $ac_model/HCLG.fst \
            $ac_model/words.txt '1:2:3:4:5' ark,t:$decode_dir/trans.txt \
            ark,t:$decode_dir/ali.txt $trans_matrix;;

    *)
        echo "Invalid test mode! Should be either \"live\" or \"simulated\"!";
        exit 1;;
esac

# Estimate the error rate for the simulated decoding
if [ $test_mode == "simulated" ]; then
    # Convert the reference transcripts from symbols to word IDs
    sym2int.pl -f 2- $ac_model/words.txt < $audio/trans.txt > $decode_dir/ref.txt

    # Compact the hypotheses belonging to the same test utterance
    cat $decode_dir/trans.txt |\
        sed -e 's/^\(test[0-9]\+\)\([^ ]\+\)\(.*\)/\1 \3/' |\
        gawk '{key=$1; $1=""; arr[key]=arr[key] " " $0; } END { for (k in arr) { print k " " arr[k]} }' > $decode_dir/hyp.txt

   # Finally compute WER
   compute-wer --mode=present ark,t:$decode_dir/ref.txt ark,t:$decode_dir/hyp.txt
fi
```

然后运行 ./run.sh 就能对wav文件进行识别了。



## 效果演示

### 正确文本

> 绿 是 阳春 烟 景 大块 文章 的 底色 四月 的 林 峦 更是 绿 得 鲜活 秀媚 诗意 盎然
>
> 他 仅 凭 腰部 的 力量 在 泳道 上下 翻腾 蛹 动 蛇行 状 如 海豚 一直 以 一头 的 优势 领先
>
> 企业 依靠 技术 挖潜 增效 他 负责 全厂 产品质量 与 技术培训 成了 厂里 的 大忙人
>
> 菜 做好 了 一碗 清蒸 武昌鱼 一碗 蕃茄 炒鸡蛋 一碗 榨菜 干 子 炒肉丝

### 识别结果

![单音节模型](https://o05g5zevc.qnssl.com/9836e5fc-4d2b-42af-8295-18703f130f05/%E5%8D%95%E9%9F%B3%E8%8A%82.png)

可以看出来，识别效果并不是很理想，性能还有待进一步提升。
