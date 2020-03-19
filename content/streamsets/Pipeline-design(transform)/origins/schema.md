# 模式推断

在处理包含带有数据的模式的数据格式（例如Avro，ORC和Parquet）时，Transformer原始使用这些模式来处理数据。

对于所有其他数据格式，Transformer起源推断源数据的架构。最佳实践是在构建管道时验证是否可以按预期推断数据的架构。

[预览管道](https://streamsets.com/documentation/controlhub/latest/help/transformer/Preview/Preview-Overview.html#concept_cgw_lkb_ghb)是确定原点如何推断模式的最简单方法。

推断数据可能需要Transformer对数据进行完整读取，然后再进行处理，以确定歧义字段的正确数据类型，因此使用自定义架构可以提高性能。

**提示：**当起点不正确地推断出架构时，您可以定义一个[自定义架构](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ntb_ttd_hhb)以供起点使用。