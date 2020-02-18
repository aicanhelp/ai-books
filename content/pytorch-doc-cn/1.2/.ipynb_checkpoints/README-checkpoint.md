# <center>PyTorch 1.2 中文文档 & 教程</center>

<center>PyTorch 是一个针对深度学习, 并且使用 GPU 和 CPU 来优化的 tensor library (张量库)</center>

## 目录结构

* [介绍](README.md)
* 第一部分：中文教程
    * 第一章：入门
        * [1.1、PyTorch 深度学习: 60 分钟极速入门]
            * [1.1.1、什么是PyTorch？](ch01-beginner/blitz/1-tensor_tutorial.ipynb)
            * [1.1.2、Autograd：自动求导](ch01-beginner/blitz/2-autograd_tutorial.ipynb)
            * [1.1.3、神经网络](ch01-beginner/blitz/3-neural_networks_tutorial.ipynb)
            * [1.1.4、训练分类器](ch01-beginner/blitz/4-cifar10_tutorial.ipynb)
            * [1.1.5、可选: 数据并行处理](ch01-beginner/blitz/5-data_parallel_tutorial.ipynb)
        * [1.2、数据加载和处理教程](ch01-beginner/2-data_loading_tutorial.ipynb)
        * [1.3、用例子学习 PyTorch](ch01-beginner/3-pytorch_with_examples.ipynb)
        * [1.4、迁移学习教程](ch01-beginner/4-transfer_learning_tutorial.ipynb)
        * [1.5、利用 TorchScript 部署 Seq2Seq 模型](ch01-beginner/5-deploy_seq2seq_hybrid_frontend_tutorial.ipynb)
        * [1.6、使用 TensorBoard 可视化模型，数据和训练](ch01-beginner/6-tensorboard_tutorial.ipynb)
        * [1.7、保存和加载模型](ch01-beginner/7-saving_loading_models.ipynb)
        * [1.8、torch.nn 到底是什么？](ch01-beginner/8-nn_tutorial.ipynb)
    * 第二章：图片
        * [2.1、TorchVision 对象检测微调教程](ch02-image/1-torchvision_tutorial.ipynb)
        * [2.2、微调Torchvision模型](ch02-image/2-finetuning_torchvision_models_tutorial.ipynb)
        * [2.3、空间变压器网络教程](ch02-image/3-spatial_transformer_tutorial.ipynb)
        * [2.4、使用PyTorch进行神经网络传递](ch02-image/4-neural_style_tutorial.ipynb)
        * [2.5、对抗性示例生成](ch02-image/5-fgsm_tutorial.ipynb)
        * [2.6、DCGAN教程](ch02-image/6-dcgan_faces_tutorial.ipynb)
    * 第三章：音频
        * [3.1、torchaudio教程](ch03-audio/1-audio_preprocessing_tutorial.ipynb)
    * 第四章：文本
        * [4.2、NLP From Scratch: 使用char-RNN对姓氏进行分类](ch04-text/1-char_rnn_classification_tutorial.ipynb)
        * [4.3、NLP From Scratch: 生成名称与字符级RNN](ch04-text/2-char_rnn_generation_tutorial.ipynb)
        * [4.4、NLP From Scratch: 基于注意力机制的 seq2seq 神经网络翻译](ch04-text/3-seq2seq_translation_tutorial.ipynb)
        * [4.5、文本分类与TorchText ](ch04-text/4-text_sentiment_ngrams_tutorial.ipynb)
        * [4.6、语言翻译与TorchText ](ch04-text/5-torchtext_translation_tutorial.ipynb)
        * [4.7、序列到序列与nn.Transformer和TorchText建模](ch04-text/6-transformer_tutorial.ipynb)
    * 第五章：强化学习
        * [5.1、强化学习（DQN）教程](ch04-q-learning/1-reinforcement_q_learning.ipynb)
    * 第六章：在生产部署PyTorch模型
        * [6.1、部署PyTorch在Python经由REST API从Flask](ch06-deployment/1-flask_rest_api_tutorial.ipynb)
        * [6.2、介绍TorchScript](ch06-deployment/2-Intro_to_TorchScript_tutorial.ipynb)
        * [6.3、在C ++中加载TorchScript模型 ](ch06-deployment/3-cpp_export.ipynb)
        * [6.4、（可选）将模型从PyTorch导出到ONNX并使用ONNX Runtime运行	](ch06-deployment/4-super_resolution_with_onnxruntime.ipynb)
    * 第七章：并行和分布式训练
        * [7.1、模型并行化最佳实践](ch07-distributed/1-model_parallel_tutorial.ipynb)
        * [7.2、入门分布式数据并行](ch07-distributed/2-ddp_tutorial.ipynb)
        * [7.3、PyTorch编写分布式应用](ch07-distributed/3-dist_tuto.ipynb)
        * [7.4、（高级）PyTorch 1.0分布式训练与Amazon AWS](ch07-distributed/4-aws_distributed_training_tutorial.ipynb) 
    * 第八章：扩展PyTorch
        * [8.1、使用自定义 C++ 扩展算TorchScript ](ch08-extension/1-torch_script_custom_ops.ipynb)
        * [8.2、用 numpy 和 scipy 创建扩展](ch08-extension/2-numpy_extensions_tutorial.ipynb)
        * [8.3、自定义 C++ 和CUDA扩展](ch08-extension/3-cpp_extension.ipynb)
        * [8.4、使用PyTorch C++ 前端](ch08-extension/4-cpp_frontend.ipynb)
* 第二部分：中文文档
    * 1、注解
        * [1、自动求导机制](notes/autograd.ipynb)
        * [2、广播语义](notes/broadcasting.ipynb)
        * [3、CPU线程和TorchScript推理](notes/cpu_threading_torchscript_inference.ipynb)
        * [4、CUDA语义](notes/cuda.ipynb)
        * [5、扩展PyTorch](notes/extending.ipynb)
        * [6、常见问题](notes/faq.html)
        * [7、对于大规模部署的特点](notes/large_scale_deployments.ipynb)
        * [8、多处理最佳实践](notes/multiprocessing.ipynb)
        * [9、重复性](notes/randomness.ipynb)
        * [10、序列化语义](notes/serialization.ipynb)
        * [11、Windows 常见问题](notes/windows.ipynb)
    * 2、社区
        * [1、PyTorch贡献说明书](community/contribution_guide.ipynb)
        * [2、PyTorch治理](community/governance.ipynb)
        * [3、PyTorch治理感兴趣的人](community/persons_of_interest.ipynb)
    * 3、封装参考文献
        * [torch](torch.html)
        * [torch.Tensor](tensors.html)
        * [Tensor Attributes](tensor_attributes.html)
        * [Type Info](type_info.html)
        * [torch.sparse](sparse.html)
        * [torch.cuda](cuda.html)
        * [torch.Storage](storage.html)
        * [torch.nn](nn.html)
        * [torch.nn.functional](nn.functional.html)
        * [torch.nn.init](nn.init.html)
        * [torch.optim](optim.html)
        * [torch.autograd](autograd.html)
        * [torch.distributed](distributed.html)
        * [torch.distributions](distributions.html)
        * [torch.hub](hub.html)
        * [torch.jit](jit.html)
        * [torch.multiprocessing](multiprocessing.html)
        * [torch.random](random.html)
        * [torch.utils.bottleneck](bottleneck.html)
        * [torch.utils.checkpoint](checkpoint.html)
        * [torch.utils.cpp_extension](cpp_extension.html)
        * [torch.utils.data](data.html)
        * [torch.utils.dlpack](dlpack.html)
        * [torch.utils.model_zoo](model_zoo.html)
        * [torch.utils.tensorboard](tensorboard.html)
        * [torch.onnx](onnx.html)
        * [torch.\_\_ config\_\_](__config__.html)
    * 4、torchvision Reference
        * [torchvision](torchvision/index.html)
    * torchaudio Reference
        * [torchaudio](https://pytorch.org/audio)
    * torchtext Reference
        * [torchtext](https://pytorch.org/text)
