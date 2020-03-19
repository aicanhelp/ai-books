# Protobuf数据格式先决条件

在读取或写入protobuf数据之前，请执行以下前提条件。

数据收集器 根据Protobuf描述符文件处理数据。描述符文件（.desc）描述一种或多种消息类型。配置阶段以处理数据时，可以指定要使用的消息类型。

在处理protobuf数据之前，需完成以下任务：

- 生成protobuf描述符文件

  生成描述符文件时，需要.proto文件来定义消息类型和任何相关的依赖关系。

  要生成描述符文件，请使用`protoc` 带有`descriptor_set_out`标志的protobuf 命令和要使用的.proto文件，如下所示：`protoc --include_imports --descriptor_set_out=.desc .proto .proto .proto...`

  例如，以下命令创建一个Engineer描述符文件，该文件基于来自Engineer，Person和Extension原始文件的信息来描述Engineer消息类型：`protoc --include_imports --descriptor_set_out=Engineer.desc Engineer.proto Person.proto Extensions.proto`

  有关protobuf和protoc命令的更多信息，请参见protobuf文档。

- 将描述符文件存储在Data Collector资源目录中

  将生成的描述符文件保存在`$SDC_RESOURCES` 目录中。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。。

有关处理此数据格式的来源和目的地的列表，请参见[数据格式支持](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-DataFormats/DataFormat_Title.html#concept_bcw_qzb_kv)。