# 控制字符删除

您可以使用多个阶段来从数据中删除控制字符，例如转义符或传输结束符。删除控制字符，以避免创建无效的记录。

当Data Collector删除控制字符时，它将删除ASCII字符代码0-31和127，但以下情况除外：

- 9-标签
- 10-换行
- 13-回车

您可以在以下阶段中使用“ **忽略Ctrl字符”**属性来删除控制字符：

- Amazon S3的起源
- Amazon SQS消费者来源
- Azure Data Lake Storage Gen1来源
- Azure Data Lake Storage Gen2的来源
- Azure IoT /事件中心消费者来源
- CoAP服务器来源
- 目录来源
- 文件尾源
- Google Cloud Storage的起源
- Google发布/订阅者来源
- gRPC客户端来源
- Hadoop FS起源
- Hadoop FS独立版本
- HTTP客户端来源
- HTTP服务器来源
- JMS消费者来源
- 卡夫卡消费者血统
- Kafka Multitopic消费者来源
- Kinesis消费者来源
- MapR FS来源
- MapR FS独立来源
- MapR Multitopic Streams消费者来源
- MapR流消费者来源
- MQTT订户来源
- 脉冲星消费者来源
- RabbitMQ消费者来源
- Redis消费者来源
- SFTP / FTP / FPTS客户端来源
- TCP服务器来源
- WebSocket客户端起源
- WebSocket服务器的起源
- 数据解析器处理器
- HTTP客户端处理器
- JSON解析器处理器
- 日志解析器处理器
- XML解析器处理器