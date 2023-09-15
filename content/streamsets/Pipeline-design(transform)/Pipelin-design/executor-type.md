# 执行方式

变压器管道可以以批处理或流模式运行。

创建管道时，请选择执行模式：

- 批量

  批处理管道处理单个批处理，然后停止。默认情况下，批处理管道处理批处理中的所有可用数据。但是，您可以在每个来源中配置最大批次大小，以限制该批次中处理的数据量。

  当管线停止时，Transformer将保存每个原点的偏移量。偏移量是原点停止读取的位置。如果重新启动管道，则原点将从已保存的偏移量开始读取。

  有关批处理管道的详细示例，请参见“ [批处理案例研究”](https://streamsets.com/documentation/controlhub/latest/help/transformer/GettingStarted/GettingStarted-Title.html#concept_jdx_q2r_vgb)。

- 流媒体

  流管线一直持续运行，直到您手动将其停止，以维持与原始系统的连接并定期处理数据。当您期望数据连续到达原始系统时，请使用流传输管道。

  启动流传输管道时，原始服务器将根据配置的最大批次大小创建一个初始批次。创建批次时，原点会记录偏移量。偏移量是原点停止读取的位置。

  目标将批次写入目标系统后，源将等待用户定义的时间间隔，然后从最后保存的偏移量开始创建新批次。

  手动停止管道时，Transformer会保存每个原点的偏移量。如果重新启动管道，则原点将从偏移量开始读取。

  处理完现有数据后，流传输管道通常会处理包含连续到达数据的小批量数据。当您要对较大的批次执行诸如聚合，重复数据删除或联接之类的处理时，可以使用[Window处理器](https://streamsets.com/documentation/controlhub/latest/help/transformer/Processors/Window.html#concept_dv2_c1q_xgb)来创建较大的批次。

  有关流传输管道的详细示例，请参见[流案例研究](https://streamsets.com/documentation/controlhub/latest/help/transformer/GettingStarted/GettingStarted-Title.html#concept_hls_kgr_vgb)。