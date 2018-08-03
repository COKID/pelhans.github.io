---
layout:     post
title:      "基于tensorflow 的中文语音识别"
subtitle:   "基于DeepSpeech2 论文与Aishell 语料"
date:       2018-08-03 00:15:18
author:     "Pelhans"
header-img: "img/speech_process.jpg"
header-mask: 0.3 
catalog:    true
tags:
    - speech process
---


> 目前网上关于tensorflow  的中文语音识别实现较少，而且结构功能较为简单。而百度在PaddlePaddle上的 Deepspeech2 实现功能却很强大，因此就做了一次大自然的搬运工把框架转为tensorflow....

* TOC
{:toc}

# 简介

百度开源的基于PaddlePaddle的Deepspeech2实现功能强大，简单易用，但新框架上手有难度而且使用过程中遇到了很多bug，因此萌生了转成tensorflow的想法。网上看了一圈，发现基于tensorflow的中文语音识别开源项目很少，而且功能较为简单。英语的项目倒是很多，但奈何写代码的人功力太深厚，想转成中文的很麻烦。因此本项目的目标是做一个简单易理解，方便新手入门的基于神经网络的ASR模型，同时把常用的功能加上方便参考。(实际上是代码功力太差...)

项目连接[ZASR_tensorflow](https://github.com/Pelhans/ZASR_tensorflow)

# 识别流程

## 一、语料整理

### 1.1 生成 manifest.{train,dev,test} 文件

manifest 文件包含音频文件的元信息，如文件路径、对应的文本、持续时长。存储格式为JSON格式。类似于Kaldi里的scp文件 和text文件的结合。

```python
{"audio_filepath": "/media/nlp/23ACE59C56A55BF3/wav_file/aishell/data_aishell/wav/train/S0002/BAC009S0002W0122.wav", "duration": 5.999, "text": "而对楼市成交抑制作用最大的限购"}
```

您可以通过example/aishell/run_data.sh 脚本中的第一部分生成该文件，也可以修改参数后运行data/aishell/aishell.py 来生成,同时若指定目录不包含指定文件，则自动下载语料并解压。

### 1.2 生成vocab.txt

vocab.txt 是基于字的，即统计语料文本中的汉字。通过data_utils/build_vocab.py 生成。

### 1.3 生成mean_std.npz 

mean_std.npz 是2000 个随机音频文件进行特征处理后，计算功率谱特征得到的均值和标准差，在训练将会使用它对输入的特征做归一化。由data_utils/compute_mean_std.py 生成。

## 二、特征处理

### 2.1 MFCC

MFCC 也就是梅尔倒谱系数，在理论上它的获取流程为：

* 先对语音进行预加重、分帧和加窗；（加强语音信号性能（信噪比，处理精度等）的一些预处理）    
* 对每一个短时分析窗，通过FFT得到对应的频谱；（获得分布在时间轴上不同时间窗内的频谱）    
* 将上面的频谱通过Mel滤波器组得到Mel频谱；（通过Mel频谱，将线形的自然频谱转换为体现人类听觉特性的Mel频谱）    
*  在Mel频谱上面进行倒谱分析（取对数，做逆变换，实际逆变换一般是通过DCT离散余弦变换来实现，
取DCT后的第2个到第13个系数作为MFCC系数），获得Mel频率倒谱系数MFCC，这个MFCC就是这帧语音的特征；（倒谱分析，获得MFCC作为语音特征）

在代码上已经被人包装好了，可以通过python_speech_features 中的mfcc函数直接得到音频的mfcc特征, 该函数的参数为:

```python
 mfcc(signal,samplerate=16000,winlen=0.025,winstep=0.01,numcep=13,
    nfilt=26,nfft=512,lowfreq=0,highfreq=None,preemph=0.97,
      ceplifter=22,appendEnergy=True)
```
具体的参数含义请查看[官方介绍](https://pypi.org/project/python_speech_features/0.4/)。

除了13 维 mfcc特征外，我们还通过python_speech_features 中的 delta 函数计算了mfcc的一阶差分和二阶差分特征，由此对每一帧得到了39维特征向量。以上过程由data_utils/audio_featurizer.py内的_compute_mfcc()函数实现。

为了更紧密的结合前后帧之间的关系，在实际输入中，每一帧的特征由前后n_context 的特征和本身的特征构成i，n_context 的大小可以在conf/hyparam.py内设置。即:

```python
inputs = concat(n_context * 39, current, n_context * 39)
dims_of_input = n_context * 39 * 2 + current
```

特征的n_context 连接由 data_utils/utils.py 内的 audiofile_to_input_vector()函数实现。

### 2.2 Linear specgram

通过FFT energy 计算linear specgram。其计算流程为：

* 通过numpy.lib.stride_tricks.as_strided() 函数对输入的样本进行切块得到一个个的windows。    
* 加窗，窗口采用hanning 窗，hanning 窗是一个加权余弦窗函数，是升余弦窗的特列。此处我们通过调用 numpy.hanning(N) 来使用它并计算权重，N代表窗口长度。    
* 做FFT，调用np.fft.rfft()函数做FFT。而后经计算绝对值、求平方、计算对数等处理得到 linear specgram。

该特征的计算由 data_utils/audio_featurizer.py 内的_specgram_real() 函数实现。

## 三、模型结构

### 3.1 概览

DeepSpeech2 的模型结构如下图所示:

![](/img/in-post/ZASR_introduction/arc.png)

其第一层为1D或2D的卷积神经网络，而后与BRNN或BGRU相连。其后添加了一个前瞻卷积神经网络(Lookahead CNN)，该卷积神经网络的输入除当前时间步外还结合了BRNN层的后两时间步输出。Lookahead CNN后采用全连接层对输出进行整型。得到logits 去计算ctc损失函数和解码。

在模型内我们对每一层的输入都使用了Batch Normalization 来减少输入和输出间的分布差距，增加模型的泛化能力并加速训练。

### 3.2 卷积神经网络CNN

为了方便搭建网络，我们对CNN部分整合到data_utils/network.py 中的conv2d()函数中，它包含卷积、Relu、max_pool、dropout操作。通过设定参数可快速得到指定形状、步长的卷积层。

针对输入语音特征，在输入到卷积层之前需要对其进行reshape操作与expamd_dims 操作，得到形状为[batch_size, n_steps, n_dim, in_channel] 的tensor。因为我在输入特征时已经采用了前后帧的信息，因此我把filter的height 设置为1。conv2d()函数的输出shape 为 [batch_size, height, width. out_channel]。

### 3.3 双向长短期记忆网络 BLSTM

为了更有效的学习前后帧间的信息，在CNN层后使用了三层BLSTM。打包好的BLSTM实现位于 network.py 内 BiRNN()函数。在该函数内部首先定义了一个正向的LSTM和一个反向的LSTM，而后调用 tf.nn.bidirectional_dynamic_rnn() 函数生成一个BLSTM网络。需要注意的是这个函数有一个time_major 选项，如果为True，那你的输入数据的shape[0]就应该是time_steps而不是batch_size。

### 3.4 Lookahead CNN 

前面说过该网络就是在输入上结合了BLSTM后两时间步的输出。因此理论上我们对该网络的输入进行错位叠加就可以了。。。但实现过程中发现tensor 因为在time_step 维度上形状不可知导致没有办法迭代的取计算这个叠加。有哪位大神知道的可以告诉我一声么，谢谢了。。。。因此这个Lookahead CNN我用的是普通的CNN取代替的，这样当由解决方案后可以直接替换了。

## 四、模型的训练

### 4.1 损失函数

损失函数采用CTC损失函数，直接调用ctc_ops.ctc_loss 进行计算，其输入为训练文本标签、神经网络的输出logits和 序列长度。这里需要注意的是假如在网络处理时time_step的长度发生了变化的话有可能导致小于seq_length 引发报错。

### 4.2 损失函数的优化

优化算法为Adam算法，虽然有可能得不到最优的结果，但对于新人或调参能力不强的还是超级好用的。

## 五、解码

### 5.1 ctc_ops.ctc_beam_search_decoder 解码

最简单的方式我们使用 tensorflow 自带的ctc_ops.ctc_beam_search_decoder() 函数对网络输出进行解码，该解码不使用外部的语言模型，输入为网络输出的logits 和 seq_length。

### 5.2 带语言模型的解码

在训练声学模型阶段使用的是5.1的解码方式，在实际使用过程中需要添加语言模型来提升识别的准确率。该部分由 utils/decoder/model.py 下的 decode_batch_beam_search()函数完成 。该函数接收一个2维的概率矩阵, 通过结合神经网络的输出概率和语言模型的评分来对输出进行解码。
