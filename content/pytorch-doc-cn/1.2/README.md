# <center>PyTorch 1.2 中文文档 & 教程</center>

<center>PyTorch 是一个针对深度学习, 并且使用 GPU 和 CPU 来优化的 tensor library (张量库)</center>

## 目录结构

* [介绍](README.md)
* 第一部分：中文教程
    * 第一章：入门
        * [1.0、PyTorch 深度学习: 60 分钟极速入门]
            * [1.1.1、什么是PyTorch？](ch01-beginner/blitz/1-tensor_tutorial.ipynb)
            * [1.1.2、Autograd：自动求导](ch01-beginner/blitz/2-autograd_tutorial.ipynb)
            * [1.1.3、神经网络](ch01-beginner/blitz/3-neural_networks_tutorial.ipynb)
            * [1.1.4、训练分类器](ch01-beginner/blitz/4-cifar10_tutorial.ipynb)
            * [1.1.5、可选: 数据并行处理](ch01-beginner/blitz/5-data_parallel_tutorial.ipynb)
        * [1.1、保存和加载模型](ch01-beginner/1-saving_loading_models.ipynb)
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
        * [4.1、NLP From Scratch: 使用char-RNN对姓氏进行分类](ch04-text/1-char_rnn_classification_tutorial.ipynb)
        * [4.2、NLP From Scratch: 生成名称与字符级RNN](ch04-text/2-char_rnn_generation_tutorial.ipynb)
        * [4.3、NLP From Scratch: 基于注意力机制的 seq2seq 神经网络翻译](ch04-text/3-seq2seq_translation_tutorial.ipynb)
        * [4.4、文本分类与TorchText ](ch04-text/4-text_sentiment_ngrams_tutorial.ipynb)
        * [4.5、语言翻译与TorchText ](ch04-text/5-torchtext_translation_tutorial.ipynb)
        * [4.6、聊天机器人教程](ch04-text/6-chatbot_tutorial.ipynb)
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
        * [1、自动求导机制](ch09-docs/notes/01-autograd.ipynb)
        * [2、广播语义](ch09-docs/notes/02-broadcasting.ipynb)
        * [3、CPU线程和TorchScript推理](ch09-docs/notes/03-cpu_threading_torchscript_inference.ipynb)
        * [4、CUDA语义](ch09-docs/notes/04-cuda.ipynb)
        * [5、扩展PyTorch](ch09-docs/notes/05-extending.ipynb)
        * [6、常见问题](ch09-docs/notes/06-faq.html)
        * [7、对于大规模部署的特点](ch09-docs/notes/07-large_scale_deployments.ipynb)
        * [8、多处理最佳实践](ch09-docs/notes/08-multiprocessing.ipynb)
        * [9、重复性](ch09-docs/notes/09-randomness.ipynb)
        * [10、序列化语义](ch09-docs/notes/10-serialization.ipynb)
        * [11、Windows 常见问题](ch09-docs/notes/11-windows.ipynb)
    * 2、社区
        * [1、PyTorch贡献说明书](ch09-docs/community/contribution_guide.md)
        * [2、PyTorch治理](ch09-docs/community/governance.md)
        * [3、PyTorch治理感兴趣的人](ch09-docs/community/persons_of_interest.md)
    * 3、封装参考文献
        * [torch](ch09-docs/references/torch.ipynb)
        * [torch.Tensor](ch09-docs/references/tensors.ipynb)
        * [Tensor Attributes](ch09-docs/references/tensor_attributes.ipynb)
        * [Type Info](ch09-docs/references/type_info.ipynb)
        * [torch.sparse](ch09-docs/references/sparse.ipynb)
        * [torch.cuda](ch09-docs/references/cuda.ipynb)
        * [torch.Storage](ch09-docs/references/storage.ipynb)
        * [torch.nn](ch09-docs/references/nn.ipynb)
        * [torch.nn.functional](ch09-docs/references/nn.functional.ipynb)
        * [torch.nn.init](ch09-docs/references/nn.init.ipynb)
        * [torch.optim](ch09-docs/references/optim.ipynb)
        * [torch.autograd](ch09-docs/references/autograd.ipynb)
        * [torch.distributed](ch09-docs/references/distributed.ipynb)
        * [torch.distributions](ch09-docs/references/distributions.ipynb)
        * [torch.hub](ch09-docs/references/hub.ipynb)
        * [torch.jit](ch09-docs/references/jit.ipynb)
        * [torch.multiprocessing](ch09-docs/references/multiprocessing.ipynb)
        * [torch.random](ch09-docs/references/random.ipynb)
        * [torch.utils.bottleneck](ch09-docs/references/bottleneck.ipynb)
        * [torch.utils.checkpoint](ch09-docs/references/checkpoint.ipynb)
        * [torch.utils.cpp_extension](ch09-docs/references/cpp_extension.ipynb)
        * [torch.utils.data](ch09-docs/references/data.ipynb)
        * [torch.utils.dlpack](ch09-docs/references/dlpack.ipynb)
        * [torch.utils.model_zoo](ch09-docs/references/model_zoo.ipynb)
        * [torch.utils.tensorboard](ch09-docs/references/tensorboard.ipynb)
        * [torch.onnx](ch09-docs/references/onnx.ipynb)
        * [torch.\_\_ config\_\_](__config__.html)
    * 4、torchvision Reference
        * [torchvision.datasets](ch09-docs/references/torchvision.datasets.ipynb)
        * [torchvision.models](ch09-docs/references/torchvision.models.ipynb)
        * [torchvision.transforms](ch09-docs/references/torchvision.transforms.ipynb)
        * [torchvision.utils](ch09-docs/references/torchvision.utils.ipynb)
        * [torchvision](torchvision/index.html)
    * 5、torchaudio Reference
        * [torchaudio](https://pytorch.org/audio)
    * 6、torchtext Reference
        * [torchtext](https://pytorch.org/text)
