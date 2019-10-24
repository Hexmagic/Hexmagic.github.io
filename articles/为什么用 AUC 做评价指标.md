> 原文地址 [器学习必刷题 - 基础概念篇 (1)：为什么用 AUC 做评价指标？](https://mp.weixin.qq.com/s?__biz=Mzg2MjI5Mzk0MA==&mid=2247484076&idx=1&sn=1bd00d3d6494f703cec69215609f6865&chksm=ce0b5877f97cd161bdf81855598adbd5425ff53e785e30cc652745017706d71609783657b0f8&scene=21#wechat_redirect)

针对 AUC 原理、偏差与方差等基础概念的考察，在算法面试题中是很常见的。本专题对相关考题进行了汇总和重点讲解，希望对大家面试有所帮助。

- [AUC 原理](#auc-%e5%8e%9f%e7%90%86)
- [为什么要用 AUC？](#%e4%b8%ba%e4%bb%80%e4%b9%88%e8%a6%81%e7%94%a8-auc)
  - [1. 为什么不用准确率 (accuracy)?](#1-%e4%b8%ba%e4%bb%80%e4%b9%88%e4%b8%8d%e7%94%a8%e5%87%86%e7%a1%ae%e7%8e%87-accuracy)
  - [2. 为什么不用召回率 (recall) 和精确率(precision)?](#2-%e4%b8%ba%e4%bb%80%e4%b9%88%e4%b8%8d%e7%94%a8%e5%8f%ac%e5%9b%9e%e7%8e%87-recall-%e5%92%8c%e7%b2%be%e7%a1%ae%e7%8e%87precision)
  - [3. 为什么不用 F1 分数？](#3-%e4%b8%ba%e4%bb%80%e4%b9%88%e4%b8%8d%e7%94%a8-f1-%e5%88%86%e6%95%b0)
  - [4. AUC 适用场景](#4-auc-%e9%80%82%e7%94%a8%e5%9c%ba%e6%99%af)
- [AUC 与 ROC？](#auc-%e4%b8%8e-roc)
  - [ROC 是什么？](#roc-%e6%98%af%e4%bb%80%e4%b9%88)
  - [画 roc 曲线的一个例子](#%e7%94%bb-roc-%e6%9b%b2%e7%ba%bf%e7%9a%84%e4%b8%80%e4%b8%aa%e4%be%8b%e5%ad%90)
  - [进一步理解 ROC 曲线](#%e8%bf%9b%e4%b8%80%e6%ad%a5%e7%90%86%e8%a7%a3-roc-%e6%9b%b2%e7%ba%bf)
  - [AUC 与 ROC 的联系](#auc-%e4%b8%8e-roc-%e7%9a%84%e8%81%94%e7%b3%bb)
- [AUC 怎么计算？](#auc-%e6%80%8e%e4%b9%88%e8%ae%a1%e7%ae%97)
### AUC 原理  

-------------

由于 AUC 关联的内容比较多，面试时问得也非常细，因此，我们将按照以下顺序对 AUC 进行重点介绍。

*   为什么要用 AUC？
    
*   AUC 与 ROC？
    
*   AUC 的实际含义及计算方法？
    

### 为什么要用 AUC？

#### 1. 为什么不用准确率 (accuracy)?

在二分类中，如果正反例分布不平衡，而我们对 minority class 更感兴趣，这时 accuracy 评价指标基本没有参考价值，比如欺诈检测，癌症检测等场景。举个栗子：

在测试集里，有 100 个 sample，99 个反例，只有 1 个正例。如果我的模型不分青红皂白对任意一个 sample 都预测是反例，那么我的模型的 accuracy 是 正确的个数／总个数 = 99/100 = 99%。

#### 2. 为什么不用召回率 (recall) 和精确率(precision)?

*   召回率：把实际为真值的判断为真值的概率。
    
*   精确率：判断为真值，判断正确的概率（实际也为真值）。
    
*   `recall = TP / (TP + FN)`
    
*   `precision = TP / (TP + FP)`
    

一般说来，如果想要召回的多，精确率就要下降；想要精确率提升，召回的就少。因此，召回率与精确率是鱼与熊掌不可兼得。

#### 3. 为什么不用 F1 分数？

F1-score 可以看做是 precision 和 recall 的综合评价指标，公式为：

![](https://mmbiz.qpic.cn/mmbiz_png/90dLE6ibsg0dVMSIBn1wPNTTYWxFSLwic54lnvDv5KdbADtpHVoUbsTuWFO5Jeib46rBpP9wU7iaS2PIH5gchFKumg/640?wx_fmt=png)

问题就是，如果你的两个模型，一个 precision 特别高，recall 特别低，而另一个 recall 特别高，precision 特别低的时候，F1-score 可能是差不多的，你也不能基于此来作出选择。  

#### 4. AUC 适用场景

说到这里，终于讲到我们的 AUC 了，它是一个用来评估分类模型性能的常见指标，优点是：

*   适用于正负样本分布不一致的场景；
    
*   对于分类器性能的评价，不限定单一的分类阈值；
    

不过，要真正理解地 AUC，还要从 ROC 开始讲起。

### AUC 与 ROC？

#### ROC 是什么？

ROC，全名叫做 Receiver Operating Characteristic（受试者操作曲线），其主要分析工具是一个画在二维平面上的曲线——ROC curve，其中，平面的横坐标是假正率（FPR），纵坐标是真正率 (TPR)。

几个概念简单解释下：

*   真正率 (TPR) = 灵敏度 (Sensitivity) = 召回率：把实际为真值的判断为真值的概率。
    
*   特异度 (Specificity)：把实际为假值的判断为假值的概率。
    
*   假正率 (FPR) = 1 - 特异度：把实际为假值的判断为真值的概率。
    
*   `TPR = TP / (TP+FN)`
    
*   `FPR = FP / (FP + TN`
    

ROC 曲线就是，在测试样本上根据不同的分类器阈值，计算出一系列的（FPR,TPR）点集，将其在平面图中连接成曲线，这就是 ROC 曲线。

#### 画 roc 曲线的一个例子

假设已经得出一系列样本被划分为正类的概率，然后按照大小排序，下图是一个示例，图中共有 20 个测试样本，“Class” 一栏表示每个测试样本真正的标签（p 表示正样本，n 表示负样本），“Score” 表示每个测试样本属于正样本的概率。

![](https://mmbiz.qpic.cn/mmbiz_jpg/90dLE6ibsg0dVMSIBn1wPNTTYWxFSLwic5OeQe54Gicy6OWYCVuzFXAXKpFib4BInnLLacAdvG0EQb3SEviaEA8DFRQ/640?wx_fmt=jpeg)接下来，我们从高到低，依次将 “Score” 值作为阈值 threshold，当测试样本属于正样本的概率大于或等于这个 threshold 时，我们认为它为正样本，否则为负样本。举例来说，对于图中的第 4 个样本，其 “Score” 值为 0.6，那么样本 1，2，3，4 都被认为是正样本，因为它们的 “Score” 值都大于等于 0.6，而其他样本则都认为是负样本。每次选取一个不同的 threshold，我们就可以得到一组 FPR 和 TPR，即 ROC 曲线上的一点。这样一来，我们一共得到了 20 组 FPR 和 TPR 的值，将它们画在 ROC 曲线的结果如下图：![](https://mmbiz.qpic.cn/mmbiz_jpg/90dLE6ibsg0dVMSIBn1wPNTTYWxFSLwic5m9X1doJDRPmqDn5BJicMHcGYgZjicDWrcbqwXicMH5qaTXwHS6CZARiaNg/640?wx_fmt=jpeg)

#### 进一步理解 ROC 曲线

如下面这幅图，(a) 图中实线为 ROC 曲线，线上每个点对应一个分类器阈值下 (FPR,TPR)。

![](https://mmbiz.qpic.cn/mmbiz_jpg/90dLE6ibsg0dVMSIBn1wPNTTYWxFSLwic5Tic23zP0ib6ydE8dZFyOM5zzq10Kj36xEyPNvuAcEenMdNib9ibN49FtWQ/640?wx_fmt=jpeg)

横轴表示 FPR，FPR 越大，预测正类中实际负类越多。

纵轴表示 TPR：正类覆盖率，TPR 越大，预测正类中实际正类越多。

ROC 曲线必过 (0, 0) 和(1, 1)两个点。

*   (1, 1) 点含义：判分阈值为 0，所有样本都被预测为正样本，TPR=1，FPR=1。
    
*   判分阈值逐渐增加，TPR 和 FPR 同时减小。
    
*   (0, 0) 点含义：判分阈值为 1，所有样本都预测为负样本，TPR=0，FPR=0。
    

理想目标：TPR=1，FPR=0，即图中 (0,1) 点，故 ROC 曲线越靠拢 (0,1) 点，越偏离 45 度对角线越好，Sensitivity、Specificity 越大效果越好。

#### AUC 与 ROC 的联系

虽然，ROC 曲线可以评测分类器的好坏，但是，人们总是希望能有一个具体数值来作为评价指标，于是 Area Under roc Curve(AUC) 就出现了。

顾名思义，AUC 的值就是处于 ROC curve 下方的那部分面积的大小。通常，AUC 的值介于 0.5 到 1.0 之间，较大的 AUC 代表了较好的 performance。

### AUC 怎么计算？

AUC 有什么实际含义呢？简单来说其实就是，随机抽出一对样本（一个正样本，一个负样本），然后用训练得到的分类器来对这两个样本进行预测，预测得到正样本的概率大于负样本概率的概率，这个概率值就是 AUC。

有以下两种方式计算 AUC：

（1）方法一：

在有 M 个正样本, N 个负样本的数据集里。一共有`M*N`对样本（一对样本即，一个正样本与一个负样本）。统计这`M*N`对样本里，正样本的预测概率大于负样本的预测概率的个数。

![](https://mmbiz.qpic.cn/mmbiz_png/90dLE6ibsg0dVMSIBn1wPNTTYWxFSLwic56mlLp39qtib4nHFgSKphJmgxibtiacLSElb05Q9ibp2c9iczTOPibCZ6Dw8w/640?wx_fmt=png)

其中，

![](https://mmbiz.qpic.cn/mmbiz_png/90dLE6ibsg0dVMSIBn1wPNTTYWxFSLwic58KGOTzPuYjFGMWIhtYD5lOpOSIGJDpicqrPBylaMG1B1wBgfrX7kJmg/640?wx_fmt=png)

计算复杂度：O(M*N)。

（2）方法二：

![](https://mmbiz.qpic.cn/mmbiz_png/90dLE6ibsg0dVMSIBn1wPNTTYWxFSLwic5eOPIxx6fU1GHZgsvDUHkLicG6ojatMfhqfzuOoWic5mOVwfmEMQeft6w/640?wx_fmt=png)

其中：

（1）M, N 分别是正样本和负样本的个数；

（2）![](https://mmbiz.qpic.cn/mmbiz_png/90dLE6ibsg0dVMSIBn1wPNTTYWxFSLwic5WsUSan0yG6ZIIXUw09b0LM5RBFsCUDoOVomcB0Jgxeh3fwM5gvZyZw/640?wx_fmt=png)代表第 i 条样本的序号。（概率得分从小到大排，排在第 rank 个位置）

（3）![](https://mmbiz.qpic.cn/mmbiz_png/90dLE6ibsg0dVMSIBn1wPNTTYWxFSLwic5Xe7MzTLlIAicuVqY3Vqob1KicQwBNSxN4heGJKXleTH35nNIYRIVwXbg/640?wx_fmt=png)表示把正样本的序号加起来。
