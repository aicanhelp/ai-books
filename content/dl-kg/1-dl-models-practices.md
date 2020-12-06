# 练手|常见26种深度学习模型的实现



昨天发了NLP中常见任务的[练手项目](https://zhuanlan.zhihu.com/p/51279338)，公众号后台爆炸了，收到几百条回复，感谢大家的关注。为了更满足大家的需求，我基本上把所有回复都扫一遍，也有人私我多更新类似的，所以今天更新关于常见深度学习模型适合练手的项目。



这些项目大部分是我之前整理的，基本上都看过，大概俩特点：代码不长，一般50-200行代码，**建议先看懂然后再实现和优化，我看基本上所有的实现都有明显可优化的地方**；五脏俱全，虽然代码不长，但是**该有的功能都有，该包含的部分也基本都有**。所以很适合练手。



另外，**对话系统方面的整理（暂定DST）下周二之前就会出来**，大家不要急，你们催更的我都有看到，放心就好。对话系统这方面，我**会努力出个最全的系列，**欢迎对这方面感兴趣的找我一起学习和讨论。



本文包括简介、练手项目和我的建议（建议最好看看这部分）。

**1.简介**

本篇是 [一文看懂深度学习发展史和常见26个模型](https://zhuanlan.zhihu.com/p/50967380) 的姐妹篇，建议先那篇再看本篇。



**2. 练手项目**

**2.1 Feed forward neural networks (FF or FFNN) and perceptrons (P)**

**前馈神经网络和感知机，**信息从前（输入）往后（输出）流动，一般用反向传播（BP）来训练。算是一种**监督学习。**

对应的代码[danijar/layered](https://link.zhihu.com/?target=https%3A//github.com/danijar/layered)；[civisanalytics/muffnn](https://link.zhihu.com/?target=https%3A//github.com/civisanalytics/muffnn)**。**



**2.2 Radial basis function (RBF)**

**径向基函数网络，**是一种径向基函数作为激活函数的FFNNs（前馈神经网络）。

对应的代码[eugeniashurko/rbfnnpy](https://link.zhihu.com/?target=https%3A//github.com/eugeniashurko/rbfnnpy)。



**2.3 Hopfield network (HN)**

**Hopfield网络，**是一种每个神经元都跟其它神经元相连接的神经网络。

对应的代码[yosukekatada/Hopfield_network](https://link.zhihu.com/?target=https%3A//github.com/yosukekatada/Hopfield_network)。



**2.4 Markov chains (MC or discrete time Markov Chain, DTMC)**

**马尔可夫链 或离散时间马尔可夫链，**算是BMs和HNs的雏形。

对应的代码Markov chains： [jsvine/markovify](https://link.zhihu.com/?target=https%3A//github.com/jsvine/markovify)， DTMC：[AndrewWalker/dtmc](https://link.zhihu.com/?target=https%3A//github.com/AndrewWalker/dtmc)。



**2.5 Boltzmann machines (BM)**

**玻尔兹曼机，**和**Hopfield网络**很类似，但是：一些神经元作为输入神经元，剩余的是隐藏层。

对应的代码[monsta-hd/boltzmann-machines](https://link.zhihu.com/?target=https%3A//github.com/monsta-hd/boltzmann-machines)。



**2.6 Restricted Boltzmann machines (RBM)**

**受限玻尔兹曼机，和玻尔兹曼机** 以及 **Hopfield网络** 都比较类似。

对应的代码[echen/restricted-boltzmann-machines](https://link.zhihu.com/?target=https%3A//github.com/echen/restricted-boltzmann-machines)。



**2.7 Autoencoders (AE)**

**自动编码**，和FFNN有些类似，它更像是FFNN的另一种用法，而不是本质上完全不同的另一种架构。

对应的代码[https://github.com/caglar/autoencoders/blob/master/ae.py](https://link.zhihu.com/?target=https%3A//github.com/caglar/autoencoders/blob/master/ae.py)。



**2.8 Sparse autoencoders (SAE)**

**稀疏自动编码，**跟自动编码在某种程度比较相反。

对应的代码[https://github.com/caglar/autoencoders/blob/master/sa.py](https://link.zhihu.com/?target=https%3A//github.com/caglar/autoencoders/blob/master/sa.py)。



**2.9 Variational autoencoders (VAE)**

**变分自动编码**，和AE架构相似，不同的是：输入样本的一个近似概率分布。这使得它跟BM、RBM更相近。

对应的代码[mattjj/svae](https://link.zhihu.com/?target=https%3A//github.com/mattjj/svae)。



**2.10 Denoising autoencoders (DAE)**

**去噪自动编码**，也是一种自编码机，它不仅需要训练数据，还需要带噪音的训练数据**。**对应对应的代码[https://github.com/caglar/autoencoders/blob/master/da.py](https://link.zhihu.com/?target=https%3A//github.com/caglar/autoencoders/blob/master/da.py)。



**2.11 Deep belief networks (DBN）**

**深度信念网络**，由多个受限玻尔兹曼机或变分自动编码堆砌而成。

对应的代码[albertbup/deep-belief-network](https://link.zhihu.com/?target=https%3A//github.com/albertbup/deep-belief-network)。



**2.12 Convolutional neural networks (CNN or deep convolutional neural networks, DCNN)**

**卷积神经网络**，这个不解释也都知道。

对应的代码：

CNN：[https://github.com/bamtercelboo/cnn-lstm-bilstm-deepcnn-clstm-in-pytorch/blob/master/models/model_CNN.py](https://link.zhihu.com/?target=https%3A//github.com/bamtercelboo/cnn-lstm-bilstm-deepcnn-clstm-in-pytorch/blob/master/models/model_CNN.py)，

DCNN：[https://github.com/bamtercelboo/cnn-lstm-bilstm-deepcnn-clstm-in-pytorch/blob/master/models/model_DeepCNN.py](https://link.zhihu.com/?target=https%3A//github.com/bamtercelboo/cnn-lstm-bilstm-deepcnn-clstm-in-pytorch/blob/master/models/model_DeepCNN.py)。



**2.13 Deconvolutional networks (DN)**

**去卷积网络**，又叫逆图形网络，是一种逆向的卷积神经网络。

对应的代码[ifp-uiuc/anna](https://link.zhihu.com/?target=https%3A//github.com/ifp-uiuc/anna)。



**2.14** **Deep convolutional inverse graphics networks (DCIGN)**

**深度卷积逆向图网络**，实际上是VAE，且分别用CNN、DNN来作编码和解码。

对应的代码[yselivonchyk/TensorFlow_DCIGN](https://link.zhihu.com/?target=https%3A//github.com/yselivonchyk/TensorFlow_DCIGN)。



**2.15 Generative adversarial networks (GAN)**

**生成对抗网络**，Goodfellow的封神之作，这个模型不用解释也都知道。

对应的代码[devnag/pytorch-generative-adversarial-networks](https://link.zhihu.com/?target=https%3A//github.com/devnag/pytorch-generative-adversarial-networks)。



**2.16 Recurrent neural networks (RNN)**

**循环神经网络**，这个更不用解释，做语音、NLP的没有人不知道，甚至非AI相关人员也知道。

对应的代码[farizrahman4u/recurrentshop](https://link.zhihu.com/?target=https%3A//github.com/farizrahman4u/recurrentshop)。



**2.17 Long / short term memory (LSTM)**

**长短期记忆网络，** RNN的变种，解决梯度消失/爆炸的问题，也不用解释，这几年刷爆各大顶会。

对应的代码[https://github.com/bamtercelboo/cnn-lstm-bilstm-deepcnn-clstm-in-pytorch/blob/master/models/model_LSTM.py](https://link.zhihu.com/?target=https%3A//github.com/bamtercelboo/cnn-lstm-bilstm-deepcnn-clstm-in-pytorch/blob/master/models/model_LSTM.py)。



**2.18 Gated recurrent units (GRU)**

**门循环单元**，类似LSTM的定位，算是LSTM的简化版**。**

对应的代码[https://github.com/bamtercelboo/cnn-lstm-bilstm-deepcnn-clstm-in-pytorch/blob/master/models/model_GRU.py](https://link.zhihu.com/?target=https%3A//github.com/bamtercelboo/cnn-lstm-bilstm-deepcnn-clstm-in-pytorch/blob/master/models/model_GRU.py)。



**2.19 Neural Turing machines (NTM)**

**神经图灵机**，LSTM的抽象，以窥探LSTM的内部细节。具有读取、写入、修改状态的能力。

对应的代码[MarkPKCollier/NeuralTuringMachine](https://link.zhihu.com/?target=https%3A//github.com/MarkPKCollier/NeuralTuringMachine)。



**2.20 Bidirectional recurrent neural networks, bidirectional long / short term memory networks and bidirectional gated recurrent units (BiRNN, BiLSTM and BiGRU respectively)**

**双向循环神经网络、双向长短期记忆网络和双向门控循环单元**，把RNN、双向的LSTM、GRU双向，不再只是从左到右，而是既有从左到右又有从右到左。

对应的代码：

BiRNN：[cstghitpku/cnn-lstm-bilstm-deepcnn-clstm-in-pytorch](https://link.zhihu.com/?target=https%3A//github.com/cstghitpku/cnn-lstm-bilstm-deepcnn-clstm-in-pytorch/tree/master/models)。

BiLSTM：[https://github.com/bamtercelboo/cnn-lstm-bilstm-deepcnn-clstm-in-pytorch/blob/master/models/model_BiLSTM.py](https://link.zhihu.com/?target=https%3A//github.com/bamtercelboo/cnn-lstm-bilstm-deepcnn-clstm-in-pytorch/blob/master/models/model_BiLSTM.py)。

BiGRU：[https://github.com/bamtercelboo/cnn-lstm-bilstm-deepcnn-clstm-in-pytorch/blob/master/models/model_BiGRU.py](https://link.zhihu.com/?target=https%3A//github.com/bamtercelboo/cnn-lstm-bilstm-deepcnn-clstm-in-pytorch/blob/master/models/model_BiGRU.py)。





**2.21 Deep residual networks (DRN)**

**深度残差网络，**是非常深的FFNN，它可以把信息从某一层传至后面几层（通常2-5层）。

对应的代码[KaimingHe/deep-residual-networks](https://link.zhihu.com/?target=https%3A//github.com/KaimingHe/deep-residual-networks)。



**2.22 Echo state networks (ESN)**

**回声状态网络，**是另一种不同类型的（循环）网络。

对应的代码[m-colombo/Tensorflow-EchoStateNetwork](https://link.zhihu.com/?target=https%3A//github.com/m-colombo/Tensorflow-EchoStateNetwork)。



**2.23 Extreme learning machines (ELM)**

**极限学习机，**本质上是随机连接的FFNN。

对应的代码[dclambert/Python-ELM](https://link.zhihu.com/?target=https%3A//github.com/dclambert/Python-ELM)。



**2.24 Liquid state machines (LSM)**

**液态机**，跟ESN类似，区别是用阈值激活函数取代了sigmoid激活函数。

对应的代码[kghose/Liquid](https://link.zhihu.com/?target=https%3A//github.com/kghose/Liquid)。



**2.25 Support vector machines (SVM)**

**支持向量机**，入门机器学习的人都知道，不解释。

对应的代码[ajtulloch/svmpy](https://link.zhihu.com/?target=https%3A//github.com/ajtulloch/svmpy)。



**2.26 Kohonen networks (KN, also self organising (feature) map, SOM, SOFM)**

**Kohonen 网络**，也称之为自组织（特征）映射。

对应的代码

KN/SOM：[mljs/som](https://link.zhihu.com/?target=https%3A//github.com/mljs/som)。



**3. 后续建议**

我个人感觉能力提升最快的方式是：先横向学习一个领域，做到全面的认识；然后从头到尾一项一项去突破，做到有深度。**如果今天学点这个，明天学点那个，水平提升很慢，建议顺着技术发展的主线从头到尾学完**。技术是无止境的，积累很重要，但有量远远不够，还得讲究方法。

对应到本文，学会并实现和优化这些模型，远远不够。我建议还可以有如下尝试：

3.1 单层模型实现之后，试试多层或者模型stack；

3.2 试试模型的结合，比如LSTM/GRU+CNN/DCNN、CNN/DCNN+LSTM/GRU、LSTM/GRU+CRF等；

3.3 在一些模型上加attention（这里很多模型适合加）；

3.4 利用这些模型解决一些比较简单的小问题，比如用CNN识别数字、LSTM+CRF做NER等；

3.5 性能方面的提升，比如支持分布式训练、支持GPU等；

3.6 把这些模型做成一个框架，到时候记得通知我，我一定拜读。



如果有实现过程中有不明白的，欢迎找我讨论。也欢迎大家关注公众号：***AI部落联盟，\***专注于NLP、DL、RL、TL、ML领域的优质内容创作。