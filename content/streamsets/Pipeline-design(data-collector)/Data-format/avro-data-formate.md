# Avro数据格式

Data Collector可以读取和写入Avro数据。

## 读取Avro数据

读取Avro数据时，基于文件和对象的来源（例如Directory和Amazon S3来源）会 为已处理的文件或对象中的每个Avro记录生成一个 数据收集器记录。

基于消息的来源，例如Kafka使用者或TCP Server的来源，会 为每个已处理的消息生成一个数据收集器记录。

读取Avro数据的处理器将生成记录，如处理器概述中所述。

生成的记录在`avroSchema` [记录头属性中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)包括Avro模式 。它们还包括一个 `precision`和 `scale` [领域属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)为每个小数场。

您可以将大多数阶段配置为使用存储在以下位置之一中的Avro模式：

- 一个`avroSchema`记录头属性
- 舞台配置属性
- 融合架构注册表

某些阶段需要将Avro模式存储在特定位置。

某些阶段无需额外配置即可读取由Avro支持的压缩编解码器压缩的数据。您可以配置一些阶段来读取其他编解码器压缩的数据。

有关每个阶段如何读取Avro数据的详细信息，请参阅阶段文档中的“数据格式”。有关读取Avro数据的阶段的列表，请参见[按阶段显示数据格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-DataFormats/DataFormat_Title.html#concept_jn1_nzb_kv)。

## 写入Avro数据

写入Avro数据时，目标和处理器将根据Avro架构写入数据。Avro架构可以位于以下位置之一：

- 一个`avroSchema`记录头属性
- 舞台配置属性
- 融合架构注册表

**提示：**必要时，可以使用[模式生成器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/SchemaGenerator.html#concept_rfz_ks3_x1b)处理器生成Avro模式，并将该模式写入`avroSchema`记录头属性。

某些阶段会在输出中自动包含Avro模式。可以将其他阶段配置为在输出中包括Avro模式。您可以使用Avro支持的压缩编解码器压缩输出数据。

有关每个阶段如何写入Avro数据的详细信息，请参阅目标文档中的“数据格式”。有关写入Avro数据的阶段的列表，请参见[按阶段显示数据格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-DataFormats/DataFormat_Title.html#concept_jn1_nzb_kv)。