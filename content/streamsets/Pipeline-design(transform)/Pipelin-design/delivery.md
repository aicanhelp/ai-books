# 交货保证

Transformer的[偏移量处理](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Offsets.html#concept_a4b_hkw_gjb)可确保在突然发生故障时，Transformer管道不会丢失数据-它至少处理一次数据。如果在特定时间发生突发故障，则最多可以重新处理一批数据。这是一次至少一次的交货保证。

当管道正常停止时，Transformer只会处理一次数据。例如，当管道以批处理模式运行并完成所有处理，管道因错误而停止或手动停止管道并允许Transformer将管道转换为已停止的管道状态时，就会发生平稳停止。

至少一次交货保证适用于导致管道突然停止的故障。这包括在不先停止Transformer的情况下强制停止管道或关闭Transformer机器。

在突然发生故障后重新启动管道时，如果管道包含存储偏移量的原点，则Transformer将从最后保存的偏移量开始处理。Transformer在从目标系统收到写入确认后提交偏移量。如果管道原点[不存储offset](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Offsets.html#concept_a4b_hkw_gjb__dd-NoOffsets)，则将按预期重新处理所有数据。

根据发生故障的时间，交付保证的结果会有所不同：

- 写入一批数据时

  当发生突发性故障的变压器将数据写入到目标系统，变压器重新处理数据。当管道再次启动时，Transformer将重新处理正在写入的数据批，因为它从未完成写入，未收到确认或存储该批处理的偏移量。结果，最后保存的偏移量指示未处理该批次。

  在这种情况下，来自批处理的已写入数据将被重新处理并再次写入目标系统。此批处理后处理的数据仅写入一次。

- 写完之后，在收到写确认之前

  当后发生突发故障变压器将数据写入到目标系统，但接收写确认并承诺补偿之前，变压器重新处理数据。当管道再次启动时，由于尚未保存偏移量，所以Transformer开始使用已写入的批处理。

  在这种情况下，整个批次将被重新处理并再次写入目标系统。此批处理后处理的数据仅写入一次。

  

- 所有其他时间

  如果在任何其他时间发生突发故障（例如在读取或处理批处理时），则Transformer会提供一次准确的行为。在这种情况下，尚未将正在运行的批次写入目标系统。当管道再次启动时，Transformer将重新处理该批处理，然后将其完全写入目标系统一次。