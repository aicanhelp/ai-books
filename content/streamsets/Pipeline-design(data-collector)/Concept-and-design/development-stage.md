# 发展阶段

您可以使用几个开发阶段来帮助开发和测试管道。

**注意：**不要在生产管道中使用开发阶段。

在开发或测试管道时，可以使用以下阶段：

- 开发数据生成器来源

  生成具有指定字段名称和字段类型的记录。对于字段类型，您可以选择一种数据类型，例如String和Integer，或者一种数据类型，例如地址信息，电话号码和书名。

  您可以使用“地图”或“列表地图”根字段类型。

  源可以生成事件以测试事件处理功能。要生成事件记录，请选择“ **产生事件”**属性。

  生成事件时，源将配置的字段用作事件记录的主体，并添加事件记录头属性。您还可以使用“ **事件名称”**属性指定事件类型。例如，要创建一个no-more-data事件，请为事件名称输入“ no-more-data”。有关事件框架和事件记录头属性的更多信息，请参见“ [数据流触发器概述”](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

  源也可以生成多个线程以测试多线程管道。若要生成多个线程，请为“ **线程数”**属性输入一个大于1的 **数字**。有关多线程管道的更多信息，请参见《[多线程管道概述》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Multithreaded_Pipelines/MultithreadedPipelines.html#concept_zpp_2xc_py)。

  记录错误属性在此阶段无效。

- 开发随机记录来源

  生成具有配置数量的Long字段的记录。您可以定义批处理之间的延迟以及要生成的最大记录数。

  记录错误属性在此阶段无效。

- 开发原始数据源来源

  根据用户提供的数据生成记录。您可以输入原始数据，选择数据的数据格式，然后配置任何与格式相关的配置选项。

  例如，您可以输入一组日志数据，选择日志数据格式，然后定义该数据的日志格式和其他日志属性。

  在数据预览中，此阶段显示原始源数据以及该阶段生成的数据。

  源可以生成事件以测试事件处理功能。要生成事件记录，请选择“ **产生事件”**属性。

- 具有缓冲源的Dev SDC RPC

  从SDC RPC目标接收记录，在将记录传递到管道的下一阶段之前，将记录临时缓冲到磁盘。用作SDC RPC目标管道中的源。**注意：** 在有意或意外停止管道之后，缓冲的记录将丢失。

  ![img](imgs/icon-Edge-20200310105633881.png)在Data Collector Edge管道中不可用。

- 开发快照重播原点

  从下载的快照文件中读取记录。原点可以从快照文件中的第一组记录开始读取。或者，您可以将原点配置为从快照文件中的特定阶段开始读取。

  ![img](imgs/icon-Edge-20200310105633881.png)在Data Collector Edge管道中不可用。

- 传感器读取器原点

  生成具有以下数据类型之一的记录：大气数据，例如BMxx80大气传感器生成的数据。记录包括以下字段：温度_C，压力_KPa和湿度。例如：`{"temperature_C": "12.34", "pressure_KPa": "567.89", "humidity": "534.44"}`Raspberry Pi系列单板计算机上的CPU温度数据，例如由BCM2835板载热传感器生成的数据。记录包含一个字段：temperature_C。例如：`{"temperature_C": "123.45"}`

  ![img](imgs/icon-SDC.png)在Data Collector管道中不可用。

- 开发者身份处理器

  将所有记录传递到下一个阶段。在管道中用作占位符。您可以定义必填字段和前提条件，并配置阶段错误处理。

- 开发随机误差处理器

  生成错误记录，以便您可以测试管道错误处理。您可以配置阶段以丢弃记录，定义必填字段和前提条件以及配置阶段错误处理。

- 开发记录创建器处理器

  为进入阶段的每个记录生成两个记录。您可以定义必填字段和前提条件，并配置阶段错误处理。

  ![img](imgs/icon-Edge-20200310105633881.png)在Data Collector Edge管道中不可用。

- 到活动目的地

  生成事件以测试事件处理功能。要生成事件，请选择“ **产生事件”**属性。

  目标为每个传入记录生成一个事件记录。它使用传入记录作为事件记录的主体，并添加事件记录头属性。请注意，传入记录中的任何记录头属性都可能丢失或替换。

  有关事件框架和事件记录头属性的更多信息，请参见“ [数据流触发器概述”](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。