# 任务型对话文章

任务型对话模型包括两种方法：Pipeline和End2End，前面介绍了

**问题定义和建模（**[任务型对话系统公式建模&&实例说明](https://zhuanlan.zhihu.com/p/48268358)**）、**

Pipeline方法中的**SLU（**[总结|对话系统中的口语理解技术(SLU)（一）](https://zhuanlan.zhihu.com/p/50095779)、[总结|对话系统中的口语理解技术(SLU)（二）](https://zhuanlan.zhihu.com/p/50347509)、[总结|对话系统中的口语理解技术(SLU)（三）](https://zhuanlan.zhihu.com/p/50704090)**）、**

**DST（**[一文看懂任务型对话系统中的状态追踪（DST）](https://zhuanlan.zhihu.com/p/51476362)**）、**

**DPL（**[一文看懂任务型对话中的对话策略学习（DPL）](https://zhuanlan.zhihu.com/p/52692962)**）、**

**NLG（**[总结|对话系统中的自然语言生成技术（NLG）](https://zhuanlan.zhihu.com/p/49197552)**）。**

今天简单介绍下**部分**End2End的方法（End2End的方法也有多种，比如：有的方法虽然是End2End的方法，但是还是单独设计模型的部件，不同部件解决Pipeline方法中的某个或多个模块；有的方法则是完全忽略Pipeline方法划分的多个模块，完全的End2End），后续抽时间会继续介绍。https://zhuanlan.zhihu.com/p/64965964

微软和清华开源ConvLab: 多领域端到端对话系统平台 https://zhuanlan.zhihu.com/p/63338937

总结+paper分享 | 任务型对话中的跨领域&个性化&迁移学习 https://zhuanlan.zhihu.com/p/49847530