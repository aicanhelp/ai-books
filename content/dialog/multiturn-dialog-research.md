# 多轮对话调研-知乎检索



## **列入的文章分类**

- 论文解读

- - [ACL自然语言处理（NLP）之多轮对话建模优化（Utterance ReWriter）](https://zhuanlan.zhihu.com/p/76551068)
  - [一文彻底读懂智能对话系统！当前研究综述和未来趋势](https://zhuanlan.zhihu.com/p/33300418)
  - [基于BERT的历史答案编码实现多轮会话问答](https://zhuanlan.zhihu.com/p/68481797)
  - [deep attention match 多轮对话跟踪模型（百度飞桨models）](https://zhuanlan.zhihu.com/p/82004144)
  - [使用多轮推理神经网络的对话生成](https://zhuanlan.zhihu.com/p/63241535)
  - [对话系统(Chatbot)论文串烧](https://zhuanlan.zhihu.com/p/35317776)

- 对话管理

- - [多轮对话之对话管理(Dialog Management)](https://zhuanlan.zhihu.com/p/32716205)
  - [多轮对话状态追踪（DST）--模型介绍篇](https://zhuanlan.zhihu.com/p/40988001)
  - [填槽与多轮对话，AI产品经理需要了解的AI技术概念](https://zhuanlan.zhihu.com/p/30069151)
  - [一文看懂任务型对话系统中的状态追踪（DST）](https://zhuanlan.zhihu.com/p/51476362)

- 平台架构

- - [一个中心+三大原则 -- 阿里这样做智能对话开发平台](https://zhuanlan.zhihu.com/p/53834315)

- 开源框架

- - [多轮对话框架Rasa代码解读－－训练过程１](https://zhuanlan.zhihu.com/p/73111199)
  - [开源对话机器人平台botfront初体验](https://zhuanlan.zhihu.com/p/78363022)

**各篇文章通读**

### **[ACL | 自然语言处理（NLP）之多轮对话建模优化（Utterance ReWriter）](https://zhuanlan.zhihu.com/p/76551068)**

主要对文献《Improving Multi-turn Dialogue Modelling with Utterance ReWriter》进行介绍。在对话中引入了预处理的过程，将每句话都重写一遍，以恢复所有相关和省略的信息。预处理之后再执行下一步骤。

### **[重磅|一文彻底读懂智能对话系统！当前研究综述和未来趋势](https://zhuanlan.zhihu.com/p/33300418)**

主要针对[《A Survey on Dialogue Systems:Recent Advances and New Frontiers》](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1711.01731)进行解读。

介绍了对话系统的大致分类（任务导向型，和非任务导向型（聊天机器人））。非任务导向型对话系统，目前主要有：生成方法，和基于检索的方法。

任务导向型方法可以分为管道方法，和端到端方法。

非任务导向型对话系统：

1. 基于神经生成模型：sequence-to-sequence models，dialogue context，Response Diversity，Topic and Personality，Outside Knowledge Base。
2. 基于检索的方法：单轮回复匹配，多轮回复匹配
3. 两种方法进行混合

### **[SIGIR 2019 |基于BERT的历史答案编码实现多轮会话问答](https://zhuanlan.zhihu.com/p/68481797)**

主要对论文《BERT with History Answer Embedding for Conversational Question Answering》进行了介绍。

### **[deep attention match 多轮对话跟踪模型（百度飞桨models）](https://zhuanlan.zhihu.com/p/82004144)**

深度注意力机制模型（Deep Attention Matching Network）介绍。该模型是开放领域多轮对话匹配模型。

### **[使用多轮推理神经网络的对话生成](https://zhuanlan.zhihu.com/p/63241535)**

对论文Dialog Generation Using Multi-turn Reasoning Neural Networks进行了介绍。

### **[对话系统(Chatbot)论文串烧](https://zhuanlan.zhihu.com/p/35317776)**

对对话系统中基于生成式的论文进行了综述。检索式的可以参见：[PaperWeekly 第37期 | 论文盘点：检索式问答系统的语义匹配模型（神经网络篇）](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s/nbT4GSUbgh-5d1J79IqeDA)。

### **[多轮对话之对话管理(Dialog Management)](https://zhuanlan.zhihu.com/p/32716205)**

主要介绍了基于三种方式的对话管理，分别是：structure-based approaches（Key Pharse Reactive Approaches [AIML]，Trees and FSM-based approach）；Principle-based approaches （Frame-based approach ，Agenda + Frame(CMU Communicator)，Information-State Approaches，Plan-based Approaches）；Statistical approaches。

相交文章本身，更多的可以关注参考链接。

### **多轮对话状态追踪（DST）--模型介绍篇**

主要介绍了多轮对话状态追踪（Dialog State Tracking）的概念，常用方法，以及对应的模型。主要涉及的参考文献为：The Dialog State Tracking Challenge Series: A Review，MACHINE LEARNING FOR DIALOG STATE TRACKING: A REVIEW。

### **[填槽与多轮对话 | AI产品经理需要了解的AI技术概念](https://zhuanlan.zhihu.com/p/30069151)**

多轮对话中填槽的概念进行了介绍，缺少相关的参考。

### **[一文看懂任务型对话系统中的状态追踪（DST）](https://zhuanlan.zhihu.com/p/51476362)**

从该文的阅读引申出相关文章：[分类和意图识别](https://zhuanlan.zhihu.com/p/50095779)，[槽填充](https://zhuanlan.zhihu.com/p/50347509)，[上下文LU和结构化LU](https://zhuanlan.zhihu.com/p/50704090)，[NLG](https://zhuanlan.zhihu.com/p/49197552)，[任务型对话系统公式建模&&实例说明](https://zhuanlan.zhihu.com/p/48268358)

详细介绍了DST，个人感觉该文章比《多轮对话状态追踪（DST）--模型介绍篇》更好。

### **[一个中心+三大原则 -- 阿里这样做智能对话开发平台](https://zhuanlan.zhihu.com/p/53834315)**

介绍阿里智能对话平台框架。

### **[多轮对话框架Rasa代码解读－－训练过程１](https://zhuanlan.zhihu.com/p/73111199)**

主要通过该文章了解到了Rasa，[https://github.com/RasaHQ/rasa](https://link.zhihu.com/?target=https%3A//github.com/RasaHQ/rasa). 对应的中文demo参见：[https://github.com/GaoQ1/rasa_chatbot_cn](https://link.zhihu.com/?target=https%3A//github.com/GaoQ1/rasa_chatbot_cn)

### **[开源对话机器人平台botfront初体验](https://zhuanlan.zhihu.com/p/78363022)**

简单的介绍了botfront平台，[https://github.com/botfront/bot](https://link.zhihu.com/?target=https%3A//github.com/botfront/botfront)