# 记忆网络之在对话系统中的应用



## **记忆网络之在对话系统中的应用**

前面几天看了下Jason Weston等人在对话系统方面的工作，可以看成是对Memory Networks的扩展吧，应用到了对话领域中，主要看了下面三篇论文，基本上是按照发表时间顺序来的，接下来我们逐篇来介绍一下其主要工作内容：

- evaluating prerequisite qualities for learning end-to-end dialog system
- Dialog-based Language Learning
- learning end-to-end goal-oriented dialog

## **evaluating prerequisite qualities for learning end-to-end dialog system**

这篇文章是15年发表的，主要贡献是提出一个评估端到端对话系统性能的指标以及相关的数据集。目前对话系统可以分成三个类别：

1，传统的对话系统中常常使用对话状态跟踪组件+对话相应生成组件来完成对话，这样就需要结合预先定义好的状态结构、用户话语、上一轮对话以及一些别的外部信息，而且每一轮对话都需要标记其内部状态并精准的分析用户意图，这就导致其很难推广到大规模应用。

2，基于神经网络的端到端对话系统，其不需要状态跟踪组件，直接根据对话的上下文和用户当前输入生成回复，并且可实现端到端的反向传播训练。但是这就导致了其没有办法使用之前的数据集进行训练和测试（都针对状态跟踪设计）。所以目前一般使用人工评分（很难扩展）或者BLEU得分来评价模型的效果，但是往往不能够达到评价的标准。

3，本文提出的数据集和模型，第二种方法的缺点主要在于BLEU在带有目的性的对话中不能够起到很好的效果，比如特定的领域，电影推荐、餐厅助手等。

本文作者认为一个对话系统应该具有下面的四种能力才可以很方便的推广到其他领域中。作者以电影推荐助手为切入点，使用OMDb、MovieLens、Reddit构建了四个数据集，分别解决四个问题。如下：

![img](https://pic3.zhimg.com/80/v2-ea5af2706911cbea92d576dd0ff516de_1440w.jpg)

1. **QA：**用于测试对话系统能否回答客观问题，类似于一个给予KB知识库的问答系统，给予SimpleQuestions数据集进行修改以适应本文的要求。其中每个问题会有一个答案列表，模型只需要生成一个答案列表即可，而不需要生成自然语言对话。最终使用@1作为评价指标。
2. **推荐：**用于测试系统对用户个性化推荐的能力，而不是像上面的通用回答一样。基于MovieLens数据集，构建user*item矩阵记录用户给电影打分，然后在生成仿真对话，对每个用户选择其打5分的电影，然后推荐一个相似的电影。其实就是讲用户喜欢的电影作为上下文，然后需要给出用户潜在喜欢的电影，如上图所示，与QA任务相同，本数据集也是生成一个答案列表即可，不过区别在于这里以@100作为评估指标而不是@1，因为推荐矩阵十分稀疏@1准确率会很低。
3. **QA+推荐混合能力：**上面两个任务都只涉及一轮对话，也就是一个问题对应一个回答，这个任务主要关注于多轮对话。每条数据会有3轮对话，第一轮是Recommendation，第二轮是QA，第三轮也是类似于Recommendation的相似电影推荐任务。每一轮的对话都需要对之前对话的理解和上下文信息的使用。而且会对三轮对话的回答都进行评估，，并且使用@10作为评价指标。
4. **chit-chat，闲聊能力：**这个数据集是为了评测对话系统的chit-chat闲聊能力。使用Reddit的电影评价回复（两个用户之间）数据构建，76%是单轮回复，17%是两轮，7%是三轮及以上。为了跟前面的几个任务匹配，这里讲对话生成转化为目标选择。即选择10000个没有在数据集中出现过的评论作为负样本，每次的目标是从10001（10000个负样本加1个正确答案）候选答案中选择正确答案。
5. **Joint task：**将上面的4个任务进行联合训练，这样我们的模型将具有chit-chat（任务4）能力和目的性回答（任务1-3）。

最终作者分别使用了Memory Networks、Supervised Embedding models、LSTMs、QA、SVD、IR等方法对上述五个任务进行了测试，发现MemNN和Supervised Embedding效果比较好，而相比之下，MemNN效果是最好的。结果如下图所示：

![img](https://pic1.zhimg.com/80/v2-aca2d62826213716a53eaafd6587a614_1440w.jpg)

本文还提出了一个比较好的想法就是将记忆分为Long-Term Memory和Short-Term Memory，其中Long指的是KB等知识库的记忆，用于回答一些客观的问题，使用三元组存储，而Short指的是当前对话的对话历史，更切合每条数据本身的记忆，可以得到一些对用户主观的了解和回答。这样综合使用Long和Short可以有比较好的回答效果，而且可以很方便地将4个任务joint起来进行训练。

![img](https://pic3.zhimg.com/80/v2-6e77324dcd14f0767f41a4f02ddea466_1440w.jpg)

## **Dialog-based Language Learning**

从论文题目可以看出，本文提出基于对话的语言学习，与此同时训练得到一个对话系统。主要贡献在于其提出了10个基于对话的监督任务数据集，如下所示：

![img](https://pic4.zhimg.com/80/v2-c30d41ef2aba2a3001bad19a1ba5f653_1440w.jpg)

1. **Imitating an Expert Student：**模仿专家进行对话，需要直接回答正确答案。可以看做剩余任务的baseline。
2. **Positive and Negative Feedback**：带有反馈的对话，回答问题会收到对或者错的提示，而且对于正确的回答会有额外的reward信号作为奖励（数据集中使用+表示）。
3. **Answers Supplied by Teacher**：当回答错误时，会被告知正确答案。难度介于任务1和2之间；
4. **Hints Supplied by Teacher**：回答错误时，会被给关于正确答案的提示，难度介于任务2和3之间；
5. **Supporting Facts Supplied by Teacher**：回答错误时，会被给关于正确答案的support facts；
6. **Missing Feedback**：有一部分正确答案的reward会丢失（50%），与任务2相对应；
7. **No Feedback**：完全没有reward；
8. **Imitation and Feedback Mixture**：任务1和2的结合，检验模型是否可以同时学习两种监督任务；
9. **Asking for Corrections**：当回答错误的时候，会主动请求帮助“Can you help me”，对应任务3；
10. **Asking for Supporting facts**：当回答错误的时候，会主动请求帮助“Can you help me”，对应任务5；

这10个监督数据及是基于bAbI和movieQA两个数据集构建的，在此之上，作者又提出了下面四种使用数据进行训练的策略：

- **Imitation Learning**
- **Reward-based Imitation**
- **Forward Prediction**
- **Reward-based Imitation + Forward Prediction**

论文仍然使用MemNN作为模型进行训练，如下图所示：

![img](https://pic1.zhimg.com/80/v2-a151ca59e5e595fa03d1cc2f1142e2a4_1440w.jpg)

## **Learning End-To-End Goal-Oriented Dialog**

这篇文章Memory Network实现了end-to-end的任务型对话系统，旨在对于Goal-Oriented的对话系统构建一个比较完善的数据集和训练方法。因为端到端的深度学习的模型在chit-chat领域已经取得了比较好的效果，但是在Goal-Oriented领域与传统模型还存在一定的Gap，本文提出了一个给予餐厅预定领域的数据集。

首先该数据集中有一个全局的知识库，主要用于保存餐厅信息（菜种类、位置、价格、评分、地址、电话、桌子大小等），该KB可以通过API调用的方式进行访问（类似于SQL查询），然后对话数据可以分为四个任务：Issuing API calls，Updating API calls，Displaying options，Providing extra information，分别对应着根据用户需求调用API（用户需要输入餐厅信息，包括上面提到的存在KB中的餐厅的几个属性），用户更改需求够重新调用API，展示满足条件的结果，最终用户确定某个餐厅后将其位置、电话等信息显示出来的四个任务。以及最中将四个任务连接在一起进行训练。数据集如下图所示：

![img](https://pic3.zhimg.com/80/v2-8020dad42ad1ebc3fbc75053099c866e_1440w.jpg)

最终每个任务的数据集除了正常构建的测试集之外还会额外构建一个OOV的测试集，以测试模型是否可以应对不在词汇表中的对话这种情况，而且为了验证模型在其他数据上的效果，作者还将DSTC2（真实的人机对话）的数据集改造成本文所需要的形式，并测试模型在该数据集上的效果。最终作者也尝试了Rule-based System、IR model、Supervised Embedding Model、Memory Networks等几个模型在本任务上面的效果，结果如下图所示：

![img](https://pic2.zhimg.com/80/v2-1a3447dbaca4b8979511ae96c3ad62e1_1440w.jpg)

本文基于TensorFlow的实现方案可以参考下面的链接：

[Memory Network实现方案](https://link.zhihu.com/?target=https%3A//github.com/vyraun/chatbot-MemN2N-tensorflow)

[Supervised Embedding Model实现方案](https://link.zhihu.com/?target=https%3A//github.com/sld/supervised-embedding-model)

上面两个实现方案均达到了论文中的效果，大概看了下代码，其实跟End-To-End MemNN基本上没有任何区别，原因就是这里所谓的对话系统并不涉及到对话生成这一块，其所回复的答案仍然是模板，也就是从候选答案池中选择一个作为答案进行回复，这跟用MemNN做QA没有本质的区别，所以模型也基本不需要改变。甚至他们的数据集构建的方法都是那么的相似==

## **总结**

但是这几篇论文怎么说呢，一方面是自己之前还没怎么接触过对话系统这个领域，看这几篇论文相当于是在入门，另外一方面可能相关的论文也好、数据集也好、模型也好都还不是怎么了解，所以整体看下来感觉这几篇论文写的有些不符合我对对话系统的认知==#然后一个就是感觉都把主要精力放在了数据集的构建上，貌似模型的创新和改动都比较少，基本上都是直接把MemNN拿过来用，答案也是模板生成，直接从候选答案中选择即可。所以整体给人的感觉就是创新性不是很大，更多的是在构建不同的数据集以寻找一种更好的对话系统的任务、模板等。