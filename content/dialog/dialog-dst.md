# 一文看懂任务型对话系统中的状态追踪（DST）

前面写了对话系统中的SLU之领域[分类和意图识别](https://zhuanlan.zhihu.com/p/50095779)、[槽填充](https://zhuanlan.zhihu.com/p/50347509)、[上下文LU和结构化LU](https://zhuanlan.zhihu.com/p/50704090)以及[NLG](https://zhuanlan.zhihu.com/p/49197552)，广受好评，很多朋友催更，感谢大家的关注和支持，今天更新任务型对话系统中的DST。DST是对话管理（DM）的一部分，而DM是任务型对话中至关重要的一部分。说个**非严格的对比**：如果把对话系统比作计算机的话，SLU相当于输入，NLG相当于输出设备，而DM相当于CPU（运算器+控制器）。



## **1. 简介**

对话系统按功能来划分的话，分为闲聊型、任务型、知识问答型和推荐型。在不同类型的聊天系统中，DM也不尽相同。

1.1. 闲聊型对话中的DM就是对上下文进行序列建模、对候选回复进行评分、排序和筛选等，以便于NLG阶段生成更好的回复；

1.2.任务型对话中的DM就是在NLU(领域分类和意图识别、槽填充)的基础上，进行对话状态的追踪（DST）以及对话策略的学习（DPL，下次分享），以便于DPL阶段策略的学习以及NLG阶段澄清需求、引导用户、询问、确认、对话结束语等。如果不太明白每个阶段的具体流程，可以看看我之前发的文章“[任务型对话系统公式建模&&实例说明](https://zhuanlan.zhihu.com/p/48268358)”。

1.3.知识问答型对话中的DM就是在问句的类型识别与分类的基础上，进行文本的检索以及知识库的匹配，以便于NLG阶段生成用户想要的文本片段或知识库实体。

1.4.推荐型对话系统中的DM就是进行用户兴趣的匹配以及推荐内容评分、排序和筛选等，以便于NLG阶段生成更好的给用户推荐的内容。



今天我们来分享任务型对话系统中的DM之DST，后续也会分享其他三类对话的DM。



## **2.DST中的状态建模和实例说明、状态表示、DSTC**

**2.1状态建模和实例说明**

何谓对话状态？其实状态St是一种**包含0时刻到t时刻的对话历史、用户目标、意图和槽值对的数据结构**，这种数据结构可以供DPL阶段学习策略（比如定机票时，是询问出发地还是确定订单？）并完成NLG阶段的回复。

![img](imgs/v2-258318fe7d1f65c5dae3d31c4ef9cec1_1440w.jpg)

对话状态追踪DST：作用是根据领域(domain)/意图(intention) 、曹植对(slot-value pairs)、之前的状态以及之前系统的Action等来追踪当前状态。它的**输入是Un（n时刻的意图和槽值对，也叫用户Action）、An-1（n-1时刻的系统Action）和Sn-1（n-1时刻的状态），输出是Sn（n时刻的状态）**。

S = {Gn,Un,Hn}，Gn是用户目标、Un同上、Hn是聊天的历史，Hn= {U0, A0, U1, A1, ... , U −1, A −1}，S =f(S −1,A −1,U )。

DST涉及到两方面内容：**状态表示、状态追踪**。另外为了解决领域数据不足的问题，DST还有很多迁移学习(Transfer Learning)方面的工作。比如基于特征的迁移学习、基于模型的迁移学习等。



为了在抽象的建模的基础上加深理解，看个小例子：

![img](imgs/v2-bd6aec9b2f142166660827d5aaab3617_1440w.jpg)

![img](imgs/v2-77cdd10bed9d338acc160fdec9400c13_1440w.jpg)

![img](imgs/v2-5b0e49fcf9f54c76eac581c4a8235cc2_1440w.jpg)



**2.2状态表示**

通过前面的建模和实例化，不难看出对话状态数跟意图和槽值对的数成**指数关系**，维护所有状态的一个分布非常非常浪费资源，因此需要比较好的状态表示法来减少状态维护的资源开销（相当于特定任务下，更合理的数据结构设计，好的数据结构带来的直接影响就是算法开销变小）。

常见的状态表示法包括两种：

**2.2.1 隐藏信息状态模型 Hidden Information State Model (HIS)**

这种方法就是：使用**状态分组**和**状态分割**减少跟踪复杂度。其实就是类似于二分查找、剪枝。

![img](imgs/v2-7223d3e7810a72feedcde3ca086f7f4c_1440w.jpg)



**2.2.2 对话状态的贝叶斯更新 Bayesian Update of Dialogue States (BUDS)**

这种方法就是：假设不同槽值的转移概率是相互独立的，或者具有非常简单的依赖关系。这样就将状态数从意图和槽值数的**指数**减少到了**线性**。

![img](imgs/v2-aeee4aaab99cb6f81418252dda985a12_1440w.jpg)

下面简单对比下两种不同状态表示法的优缺点：

![img](imgs/v2-460dd9159c43651dbee5d9b896658e8d_1440w.jpg)

**2.3 DSTC**

讲到DST就不得不讲DSTC，DSTC是[Dialog System Technology Challenge](https://link.zhihu.com/?target=https%3A//www.microsoft.com/en-us/research/event/dialog-state-tracking-challenge/)，主要包括6个Challenge。DSTC对DST的作用就相当于目标函数对机器学习任务的作用，真正起到了评估DST技术以及促进DST技术发展的作用。之所以在DST前先说DSTC是因为后面的很多DST的方法是在某个DSTC（大多是DSTC2、DSTC3、DSTC4、DSTC5）上做的。

![img](imgs/v2-7b2f26ae5bd0dee25db9af77cb727d60_1440w.jpg)



## **3. DST**

先来看看DST的形象化

![img](imgs/v2-b042353e435433ee4b5e21f3be9f9053_1440w.jpg)



再来看看我总结的DST的方法汇总，注意我没有整理基于规则的DST（**基于规则的方法虽然可以较好利用先验知识从而可以较好解决冷启动等问题，但是需要太多人工、非常不灵活、扩展性和移植性很差、不能同时追踪多种状态**）。

![img](imgs/v2-bda7871faba4b8ce11361f9d6d15a1bf_1440w.jpg)



下面分别介绍一下对话系统中的不同DST技术。

**3.1 CRF（[Lee, SIGDIAL 2013](https://link.zhihu.com/?target=http%3A//www.aclweb.org/anthology/W13-4069)）（[Kim et al., 2014](https://link.zhihu.com/?target=http%3A//www.aclweb.org/anthology/W14-4345)）**

从BUDS中对不同槽值的转移概率是相互独立的假设（是不是很像马尔可夫假设？）以及St的预测需要Un、An-1和Sn-1（转移概率和发射概率），是不是想到了HMM和CRF？没错，前期的基于统计的DST就是用了很多CRF。 n = （S −1, A −1, U ）。

![img](imgs/v2-ce790e50266d0b69cb3a0aa6b1458117_1440w.jpg)



[Lee, SIGDIAL 2013](https://link.zhihu.com/?target=http%3A//www.aclweb.org/anthology/W13-4069)的主要思想如下：

![img](imgs/v2-a524c411eb5711b7bb0d9effff94a824_1440w.jpg)

![img](imgs/v2-1d1882e8355d344a577ce6e6d1e63a19_1440w.jpg)



[Kim et al., 2014](https://link.zhihu.com/?target=http%3A//www.aclweb.org/anthology/W14-4345)的主要思想如下：

![img](imgs/v2-278c996e8572c8ee50640c7d485f03c0_1440w.jpg)

![img](imgs/v2-7846ef5729548e0af180b863d99b9085_1440w.jpg)



**3.2 NN-Based （**[Mrkšić et al., ACL 2015](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1506.07190)**）（**[Henderson et al., 2013](https://link.zhihu.com/?target=http%3A//www.anthology.aclweb.org/W/W13/W13-4073.pdf)**）（**[Henderson et al., 2014](https://link.zhihu.com/?target=http%3A//svr-ftp.eng.cam.ac.uk/~sjy/papers/htyo14.pdf)**）（**[Zilka el al., 2015](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1507.03471)**）**

关于神经网络的介绍、神经网络的好处和坏处，不再赘述，已经烂大街。基于神经网络的很多方法是在DSTC上做的，这里选取了几篇有针对性的经典论文简单介绍下。

[Mrkšić et al., ACL 2015](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1506.07190)是ACL2015的一篇论文，它是用RNN进行多领域的对话状态追踪，主要贡献是证明：利用多个领域的数据来训练一个通用的状态追踪模型比利用单领域数据训练追踪模型效果要好。

![img](imgs/v2-6d8a28840e398f6fe35f26a1abb1f895_1440w.jpg)



[Henderson et al., 2013](https://link.zhihu.com/?target=http%3A//www.anthology.aclweb.org/W/W13/W13-4073.pdf) 是利用DNN来解决DSTC，它把DST当分类问题，输入时间窗口内对话轮次提取的特征，输出slot值的概率分布。该方法不太容易过拟合，领域迁移性很好。模型结构图如下：

![img](imgs/v2-de27cf61315d9557be292f4d2a01c0ca_1440w.jpg)

![img](imgs/v2-12446cb85c6230fd40dfdbd20cd5fc76_1440w.jpg)



[Henderson et al., 2014](https://link.zhihu.com/?target=http%3A//svr-ftp.eng.cam.ac.uk/~sjy/papers/htyo14.pdf) ，基于DRNN和无监督的自适应的对话状态鲁棒性跟踪，从论文名字就能看出因为使用DRNN和无监督的自适应导致DST**鲁棒性很好**。

先来看看特征提取的办法：主要提取f，fs，fv三种特征，f是针对原始输入提取，fs和fv是对原始输入中的词做Tag替换得到**泛化特征**。

![img](imgs/v2-8d683b018170a106d44e791e3a0b411c_1440w.jpg)

再来看下模型结构：对slot训练一个模型，利用无监督的自适应学习，将模型泛化到新的domain以便于提高模型的泛化能力。

![img](imgs/v2-2da704b4e1d934c31c247be3080fe338_1440w.jpg)



[Zilka el al., 2015](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1507.03471)，基于增量LSTM在DSTC2做对话状态追踪，具体思想如下：

![img](imgs/v2-41efad4fa589c88a5c09705729eee471_1440w.jpg)



**3.3 基于迁移学习做DST （**[Williams 2013](https://link.zhihu.com/?target=http%3A//www.aclweb.org/anthology/W13-4068)**）（**[Mrkšic, ACL 2015](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1506.07190)**）**

目前对话系统数据较少，我比较看好迁移学习在任务型对话中的应用，尤其是DST这种较复杂的任务。

[Williams 2013](https://link.zhihu.com/?target=http%3A//www.aclweb.org/anthology/W13-4068)，这是通过**多领域学习与泛化**来做对话状态追踪，比较好的解决了数据目标领域数据不足的问题。

[Mrkšic, ACL 2015](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1506.07190)，这是ACL 2015的一篇paper，基于RNN做多领域的对话状态追踪，主要贡献是证明：利用多个领域的数据来训练一个通用的状态追踪模型比利用单领域数据训练追踪模型效果要好。顺便说一句，这篇论文涵盖了很多任务型对话领域比较高产的学者。

![img](imgs/v2-a897c4465d2db75fbb5d6e77b7a5beda_1440w.jpg)



**3.4 Multichannel Tracker （**[Shietal., 2016](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1701.06247)**）**

[Shietal., 2016](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1701.06247)，基于**多通道卷积神经网络**做**跨语言**的对话状态跟踪。为每一个slot训练一个多通道CNN（中文character CNN、中文word CNN、英文word CNN），然后跨语言做对话状态追踪，我个人很喜欢这篇paper，也非常推荐大家好好读读这篇paper。

先来看看方法的整体结构：

![img](imgs/v2-d3cfffb029db8a3df160b7513229a650_1440w.jpg)



再来看看多通道CNN的结构图：

![img](imgs/v2-ab0cf42056bce39c2c8916e0e2ffda4a_1440w.jpg)



最后看看输入之前的预处理：

![img](imgs/v2-b9c364bd7635d5b47ef7a0a66d704c59_1440w.jpg)

**3.5 Neural Belief Tracker （**[Mrkšić et al., ACL 2017](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1606.03777)**）**

这是发表于ACL 2017的一篇论文，个人觉得水平很高。

先来看一下基于word2vec的表示学习模型，本文提出两种架构：NBT-DNN、NBT+CNN，结构图如下：

![img](imgs/v2-f0eea905e5b6bca825efc557a9719ea3_1440w.jpg)

![img](imgs/v2-f1c4e96a063810f679f7387ea466384b_1440w.jpg)

再来看看整个模型的结构图，它包含语义解码和上下文建模两部分：语义解码：判断槽值对是否出现在当前query；上下文建模：解析上一轮系统Act，系统询问（tq）+ 系统确认（ts+tv）。

![img](imgs/v2-82a3fc1af8fb391283a882fc1064c2a4_1440w.jpg)



模型还有一部分：二元决策器，用来判定当前轮的槽值对的状态。本文的状态更新机制采用简单的基于规则的状态更新机制。

另外，ACL 2018在本文的基础上提出完全NBT（**Fully NBT）**，主要变动是修改基于规则的状态更新机制，把更新机制融合到模型来做**联合训练**。具体更新状态的机制包括One-Step Markovian Update（ 一步马尔科夫更新，使用两个矩阵学习当前状态和前一时刻状态间的更新关系和系数）和Constrained Markovian Update（约束马尔科夫更新，利用对角线和非对角线来构建前一种方法中的矩阵，对角线学习当前状态和前一时刻状态间的关系，非对角线学习不同value间如何相互影响）。总之，这个工作扩展的比较细致。



**3.6 其他方法**

其实还有很多种对话状态追踪的方法，比如基于贝叶斯网络做DST、基于POMDP（部分可观测马尔可夫决策过程）做DST等，因为时间相对比较久远，这里不再赘述。

## **4.不同DST方法的对比**

**以上介绍了多种对话系统中的DST技术，下面简单总结下它们的优势和劣势。**

![img](imgs/v2-c3a3e629d6c3328242a97e8f111ba261_1440w.jpg)



## **5.DST技术的评估**

**任何一项技术想要取得进步，那么他的评测方法是至关重要的（就相当于目标函数之于机器学习算法），所以我列出一些关于DST的评估。遗憾的是，目前DST的评估我感觉并不成熟，这也是制约DST发展的一个重要原因，如果谁能想出更好的评估方法或整理出一个业内公认的高质量数据集，那么一定会在DST（甚至是对话系统）领域有一席之地，引用量也会蹭蹭的上涨。**

![img](imgs/v2-989b408ff383e78e4db51e505bc0b1c7_1440w.jpg)



## **6.本文涉及到的Paper**

**6.1.Dialog State Tracking Challenge (DSTC)**

[Williams et al. 2013, Henderson et al. 2014, Henderson et al. 2014, Kim et al. 2016, Kim et al. 2016, Hori et al. 2017](https://link.zhihu.com/?target=https%3A//www.microsoft.com/en-us/research/event/dialog-state-tracking-challenge/)



**6.2. State Representation:**

**6.2.1 HIS**

Steve Young, Jost Schatzmann, Karl Weilhammer, and Hui Ye. The hidden information state approach to dialog management.



**6.2.2 BUDS**

Blaise Thomson, Jost Schatzmann, and Steve Young. Bayesian update of dialogue state for robust dialogue systems.



**6.3.DST**

**6.3.1 CRF**

Sungjin Lee. Structured discriminative model for dialog state tracking. In Proceedings of the SIGDIAL 2013 Conference. [Lee, SIGDIAL 2013](https://link.zhihu.com/?target=http%3A//www.aclweb.org/anthology/W13-4069)

Seokhwan Kim and Rafael E Banchs. Sequential labeling for tracking dynamic dialog states. [Kim et al., 2014](https://link.zhihu.com/?target=http%3A//www.aclweb.org/anthology/W14-4345)



**6.3.2 NN-Based DST**

Multi-domain Dialog State Tracking using Recurrent Neural Network, [Mrkšić et al., ACL 2015](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1506.07190)

Deep Neural Network Approach for the Dialog State Tracking Challenge, [Henderson et al., 2013](https://link.zhihu.com/?target=http%3A//www.anthology.aclweb.org/W/W13/W13-4073.pdf)

Robust dialog state tracking using delexicalised recurrent neural networks and unsupervised adaptation, [Henderson et al., 2014](https://link.zhihu.com/?target=http%3A//svr-ftp.eng.cam.ac.uk/~sjy/papers/htyo14.pdf)

Incremental lstm-based dialog state tracker, [Zilka el al., 2015](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1507.03471).



**6.3.3 Neural Belief Tracker**

Neural Belief Tracker: Data-Driven Dialogue State Tracking ,[Mrkšić et al., ACL 2017](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1606.03777)



**6.3.4 Multichannel Tracker**

A Multichannel Convolutional Neural Network For Cross-language Dialog State Tracking, [Shi et al., 2016](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1701.06247)



**6.3.5 Transfer learning for DST**

**6.3.5.1 Feature based transfer for DST**

Jason Williams. [Multi-domain learning and generalization in dialog state tracking](https://link.zhihu.com/?target=http%3A//www.aclweb.org/anthology/W13-4068). In Proceedings of SIGDIAL. [Williams 2013](https://link.zhihu.com/?target=http%3A//www.aclweb.org/anthology/W13-4068)

Hang Ren, Weiqun Xu, and Yonghong Yan. [Markovian discriminative modeling for cross-domain dialog state tracking](https://link.zhihu.com/?target=http%3A//www.aclweb.org/anthology/W14-4344).



**6.3.5.2 Model based transfer for DST**

Nikola Mrkšic, Diarmuid O Séaghdha, Blaise Thomson,Milica Gaši ́c, Pei-Hao Su, David Vandyke, Tsung-Hsien Wen, and Steve Young. [Multi- domain dialog state tracking using recurrent neural networks](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/1506.07190.pdf). [Mrkšic, ACL 2015](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1506.07190)



**以上，我几乎涉及到DST中的所有内容，如果看完之后不懂，欢迎找我私聊讨论。**