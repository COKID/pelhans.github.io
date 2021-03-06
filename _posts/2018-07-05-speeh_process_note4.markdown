---
layout:     post
title:      "现代语音信号处理笔记 (四)"
subtitle:   "倒谱分析与同态滤波"
date:       2018-07-05 00:15:18
author:     "Pelhans"
header-img: "img/speech_process.jpg"
header-mask: 0.3 
catalog:    true
tags:
    - speech process
---


> 本节针对《现代语音信号处理》这本书的第五章，对应倒谱分析与同态滤波部分。

* TOC
{:toc}

# 倒谱分析与同态滤波

语音信号可用一个线性时不变系统的输出表示，即**看做声门激励信号与声道冲激响应的卷积**。在语音信号处理领域，根据语音信号求解声门激励函数和声道激励相应有非常重要的意义，如要求出语音信号的共振峰(共振峰是声道传递函数个对复共轭极点的频率)，需要知道声道传递函数。

由卷积结果求出参与卷积的各信号，即将卷积分量分开，通常称为解卷，也成反卷积。**解卷算法分为两大类，第一类为参数解卷，包括LPC等。第二类为非参数解卷，同态信号处理是其中最重要的一种**。

同态信号处理也成通泰旅欧，可实现将卷积关系变为求和关系的分离处理。同态滤波是非线性滤波，但服从广义叠加原理。对语音信号进行同态分析可得到其倒谱参数，所以同态分析也成倒谱分析。倒谱参数所包含的信息比其他参数多，效果更好。

## 同态信号处理的基本原理

同态处理理论中，任何同态系统均可表示为三个子系统的级联。如下图所示：

![](/img/in-post/speech_process_note4/speech_process_note4_1.png)

下面分析同态信号处理的基本原理。假设输入信号为：

$$ x(n) = x_{1}(n)*x_{2}(n) $$

其中$x_{1}(n)$和$x_{2}(n)$分别为声门激励和声道冲激响应。特性系统D将卷积信号转换为加性信号，其包括三部分，首先进行Z变换，将卷积性信号转化为乘积性信号：

$$ Z[x(n)] = X(z) = X_{1}(z)X_{2}(n) $$

再进行对数运算，将成型运算转化为加性运算：

$$ln X(z) = lnX_{1}(z) + lnX_{2}(z) = \hat{X}_{1}(z) + \hat{X}_{2}(z) = \hat{X}(z) $$

上面的公式为加性的对数z域信号，使用起来不方便，因而将其转变为时域信号，即最后进行逆Z变换，从而：

$$ Z^{-1}[\hat{X}(z)] = Z^{-1}[\hat{X}_{1}(z) + \hat{X}_{2}(z)] = \hat{x}_{1}(n) + \hat{x}_{2}(n) = \hat{x}(n) $$


加性信号的Z变换或逆Z变换还是加性信号，因此通过对$\hat{x}(n)$这个时域信号可用线性系统来处理。处理后，若将其恢复为卷及信号，则通过上图所示的逆变换。

在上式中，**$\hat{x}(n)$为时序序列，称其为x(n)的复数倒谱，简称复倒谱**，也成为对数复倒谱，其英文为Complex Cepstrum。显然$\hat{x}(n)$所处的离散时域不同于x(n)所在的离散时域，称其为复倒谱域。绝大数数字信号处理问题中，$ X(z)、\hat{X}(z)、Y(z)、\hat{Y}(z)$的收敛域均包含单位圆，因而**上面各式总的正、反Z变换均可用DFT或IDFT替代**。

除复倒谱外还有倒谱，即将卷积分量分开上面的Z逆变换改写为：

$$ c(n) = F^{-1}[ln|X(e^{jw})|] $$

上式表明，c(n)是x(n)对数幅度谱的傅里叶逆变换。复倒谱设计了负对数运算，二倒谱只进行实数的对数运算。

## 声门激励信号的复倒谱性质

除发清音时，声门激励信号的复倒谱性质是能量较小，频谱均匀分布的白噪声外。发浊音时，声门激励是以基音周期为周期的冲激序列。即

$$ x(n) = \sum_{r=0}^{M}\alpha_{r}\delta(n-rN_{p}) $$

其中，M为正整数，且$0\le r\le M$，$\alpha_{r}$为幅度因子，$N_{p}$为基音周期。根据上面的变换过程就可以求得x(n)的复倒谱：

$$ \hat{x}(n) = ln\alpha_{0}\delta(n) - \sum_{k=1}^{\infty}[\frac{1}{k}\sum_{r=1}^{M}\alpha_{r}^{k}\delta(n-kN_{p})] $$

由上式可得出：

* 有限长度周期冲激序列，其复倒谱也是周期冲激序列，且周期不变。利用该特性，可**用高复倒谱窗从语音信号的复倒谱中提取浊音激励信号的特性，从而可用复倒谱提取基音**。    
* 其振幅随k增大而衰减。

### 声道冲激响应序列的复倒谱性质

采用最严格(也是最普遍的)极零模型描述声道冲激响应x(n)。按上述变换顺序求和复倒谱喉，可得到声道冲激响应的复倒谱性质：

* $\hat{x}(n)$为双边序列，存在于$-\infty \lt n \lt \infty $范围内。    
* $\hat{x}(n)$为衰减序列，随着
$|n
|$的增大而减小。且其衰减速度比$\frac{1}{|n|}$快，因此$\hat{x}(n)$比x(n)更集中在原点附近。因而可以**用低复倒谱窗在复倒谱域中提取声道冲击响应**。    

### 避免相位卷绕的算法

复倒谱分析中，Z变换喉得到的是复数，此时存在相位多值性问题，称为相位卷绕。相位卷绕使得后续的求复倒谱及由复倒谱恢复语音信号等运算存在不确定性，从而产生错误。常用的避免相位卷绕求复倒谱的方法有微分法、最小相位信号法、递推法等。

## Mel频率倒谱系数(MFCC)

前面介绍过Mel频率，将频率变换到Mel域后，Mel带通滤波器组的中西频率均匀排列。用Mel带通滤波器对输入信号滤波，每个频带分量的作用在人耳中是叠加的，因而将每个滤波器带内的能量叠加，即取个三角形滤波器带宽内所有信号幅度加权和作为带通滤波器组的输出，滤波器组数量一般选取12~16个，再对所有滤波器的对数幅度谱进行离散余弦变换(DCT)得到MFCC。

上述得到的MFCC特征是静态特征，进行一阶与二阶差分，可得到相应的动态特性。


# Ref

现代语音信号处理[胡航 电子工业出版社] 第五章 倒谱分析与同态滤波
