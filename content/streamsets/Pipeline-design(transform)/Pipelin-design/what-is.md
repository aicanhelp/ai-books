# 什么是变压器管道？

一个变压器管道描述数据从原点系统流动到目标系统，并定义了如何沿途转换数据。

变压器管道的设计在管道设计或变压器，并通过执行的变压器。

您可以在Transformer管道中包括以下阶段：

- 起源

  一个[起源](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/Origins-Overview.html#concept_snr_zj5_pgb)阶段代表原点系统。管道可以包含多个原始阶段。

  如果在管道中使用多个原点，则必须使用[Join处理器](https://streamsets.com/documentation/controlhub/latest/help/transformer/Processors/Join.html#concept_xdr_slq_sgb)来合并原点读取的数据。每个Join处理器可以联接来自两个输入流的数据。

- 处理器

  您可以使用多个[处理器](https://streamsets.com/documentation/controlhub/latest/help/transformer/Processors/Processors-Overview.html#concept_j43_lk5_pgb)阶段对数据执行复杂的转换。

- 目的地

  甲[目的地](https://streamsets.com/documentation/controlhub/latest/help/transformer/Destinations/Destinations-Overview.html#concept_qxy_zk5_pgb)阶段代表一个目标系统。管道可以包含多个目标阶段。

开发管道时，还可以包括开发阶段，以提供示例数据并生成错误以测试错误处理。