# 故障排除

## 访问错误消息

信息和错误消息根据信息类型显示在不同的位置：

- 管道配置问题

  该管道设计 UI提供指导和错误的详细信息如下：通过隐式验证找到的问题将显示在“问题”列表中。在发生问题的阶段或在管道配置问题的画布上显示错误图标。通过显式验证发现的问题将显示在画布上的警告消息中。

- 错误记录信息

  您可以使用“错误记录”管道属性将错误记录和相关详细信息写入[另一个系统以供查看](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_kgc_l4y_5r)。以下记录标题属性中的信息可以帮助您确定发生的问题。有关更多信息，请参见[内部属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_itf_55z_dz)。

  有关错误记录和错误记录处理的更多信息，请参见[错误记录处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_pm4_txm_vq)。

## 管道基础

使用以下技巧来获得管道基础知识的帮助：

- 当我转到Data Collector UI时，出现“网页不可用”错误消息。

  该数据收集器没有运行。启动数据收集器。

- 为什么未启用“开始”图标？

  您可以在有效时启动管道。使用“问题”图标查看管道中的问题列表。解决问题后，“开始”图标将变为启用状态。

- 为什么选择带预览数据的字段选项不起作用？没有预览数据显示。

  当管道对于数据预览有效时，并且将Data Collector配置为在后台运行预览时，“ 选择带有预览数据的字段”将起作用。确保所有阶段均已连接，并且配置了必需的属性。通过单击“ **帮助”** > **“设置”**，还可以验证预览是否在后台运行。

- 有时我会得到可用字段的列表，有时却没有。那是怎么回事？

  当管道对于数据预览有效时，并且当Data Collector配置为在后台运行预览时，管道可以显示可用字段的列表。确保所有阶段均已连接，并且配置了必需的属性。通过单击“ **帮助”** > **“设置”**，还可以验证预览是否在后台运行。

### 资料预览

使用以下提示来帮助数据预览：

- 为什么未启用“预览”图标？

  连接管道中的所有阶段并配置所需的属性后，可以预览数据。您可以将任何有效值用作所需属性的占位符。

- 数据预览为什么不显示任何数据？

  如果数据预览不显示任何数据，则可能发生以下问题之一：原点可能配置不正确。在“预览”面板中，检查“配置”选项卡以获取相关问题的由来。对于某些来源，您可以使用Raw Preview查看配置信息是否正确。该原点目前可能没有任何数据。某些来源（例如目录，文件尾和Kafka Consumer）可以显示处理后的数据以进行数据预览。但是，大多数来源都需要输入数据才能启用数据预览。

- 为什么我要求更多记录时只能预览10条记录？

  该数据采集器 最大预览批次大小覆盖数据预览批量大小。该数据采集器默认设置为10条记录。

  当请求数据预览时，可以请求最多Data Collector预览批处理大小，或者可以增加Data Collector 配置文件中的**楼的楼Preview.maxBatchSize**属性。`$SDC_CONF/sdc.properties`

- 在数据预览中，我编辑了阶段配置，并单击了“运行包含更改”，但看不到数据有任何更改。

  如果配置更改在源中，则可能会发生这种情况。使用更改运行使用现有的预览数据。若要查看对原始配置的更改如何影响预览数据，请使用“刷新预览”。

### 一般验证错误

使用以下技巧来获得有关一般管道验证错误的帮助：

- 该管道在一个阶段中具有以下一组验证错误：

  `CONTAINER_0901 - Could not find stage definition for :. CREATION_006 - Stage definition not found. Library . Stage .  Version  VALIDATION_0006 - Stage definition does not exist, library ,  name , version `

  管道使用未在Data Collector上安装的阶段。如果您从其他版本的Data Collector导入了管道并且当前的Data Collector未启用使用该阶段，则可能会发生这种情况。

  如果数据收集器使用阶段的其他版本，则可以删除无效的版本，并将其替换为本地有效的版本。例如，如果管道使用较旧版本的Hadoop FS目标，则可以用此Data Collector使用的版本替换它。

  如果需要使用未在Data Collector上安装的舞台，请安装相关的舞台库。有关安装其他驱动程序的信息，请参阅 Data Collector 文档 中的“ [安装外部库](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/ExternalLibs.html%23concept_pdv_qlw_ft) ”。

## 管道设计师

- 我有一个处于编辑模式的管道，但是管道设计器没有响应我的更改尝试。

  验证创作数据收集器是否可用。当创作数据收集器不可用时，工具栏中的创作数据收集器图标为红色，如下所示：![img](imgs/icon_AuthoringSDC_unavailable.png)。

  当创作数据收集器不可用时，您将无法编辑管道或管道片段。使用创作数据收集器图标选择可用的创作数据收集器。

## 起源

使用以下技巧来获得有关原始阶段和系统的帮助。

### 目录

- 为什么目录源不读取我的所有文件？

  目录源根据配置的文件名模式，读取顺序和要处理的第一个文件读取一组文件。如果新文件在目录原点经过读取顺序之后到达，则目录原点不会读取文件，除非您重置原点。

  使用上次修改的时间戳读取顺序时，到达的文件的时间戳应晚于目录中的文件。

  同样，使用按字典顺序升序的文件名读取顺序时，请确保文件的命名约定按字典顺序升序。例如， filename-1.log，filename-2.log等可以正常工作，直到filename-10.log为止。如果文件名-10.log目录原点完成看过之后到达的文件名- 2.登录，则该目录的起源不读文件名，10.log，因为它是字典序早于文件名- 2.登录。

  有关更多信息，请参见[阅读顺序](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Directory.html#concept_b4d_fym_xv)。

### Hadoop FS

- 在管道中，Hadoop FS源具有一个错误图标，其中包含以下消息：

  `Validation_0071 - Stage '' does not support 'Standalone' execution mode`

  您正在为独立执行模式配置的管道中使用Hadoop FS源。在群集模式管道中使用Hadoop FS源。

  解决方法：在管道属性中，将**执行模式**设置为 **Cluster**。或者，如果要以独立模式运行管道，请使用目录或文件尾源来处理文件数据。

### JDBC起源

- 我的MySQL JDBC驱动程序5.0无法验证我的JBDC查询使用者来源中的查询。

  当您在查询中使用LIMIT子句时，可能会发生这种情况。

  解决方法：升级到5.1版。

- 我正在使用JDBC源读取MySQL数据。为什么将日期时间值设置为零会像错误记录一样被对待？

  MySQL将无效日期视为异常，因此JDBC查询使用者和JDBC多表使用者都为无效日期创建错误记录。

  您可以通过在源中设置JDBC配置属性来覆盖此行为。添加zeroDateTimeBehavior属性，并将值设置为“ convertToNull”。

  有关此和其他特定于MySQL的JDBC配置属性的更多信息，请参见 http://dev.mysql.com/doc/connector-j/en/connector-j-reference-configuration-properties.html。

- 使用JDBC查询使用者来源的管道不断停止，并出现以下错误：

  `JDBC_77  attempting to execute query ''. Giving up  after  errors as per stage configuration. First error: .`

  当源无法成功执行查询时，会发生这种情况。要处理瞬时连接或网络错误，请尝试增加源的JDBC选项卡上的“查询时重试次数”属性的值。

### 卡夫卡消费者

- 为什么我的管道无法从Kafka主题中读取现有数据？

  Kafka使用者根据“自动偏移重置”属性的值确定要读取的第一条消息。使用默认值“最早”，源将读取主题中第一条消息开始的消息。如果您已经启动管道或使用其他设置运行预览，则偏移量已经提交。要读取主题中最早的未读数据，请将“ **自动偏移重置”**设置为“ **最早”**，然后临时将使用者组名称更改为其他值。运行数据预览。然后，将使用者组更改回正确的值并启动管道。

- 如何为Kafka Consumer重置偏移量？

  由于Kafka Consumer的偏移量与ZooKeeper一起存储在Kafka集群中，因此您无法通过Data Collector重置偏移量。有关通过Kafka重置偏移量的信息，请参阅Apache Kafka文档。

- 启用Kerberos的Kafka Consumer无法连接到Kafka的HDP 2.3发行版。

  启用Kerberos时，默认情况下，HDP 2.3将 **security.inter.broker.protocol** Kafka代理配置属性设置为 `PLAINTEXTSASL`，不支持。要更正此问题，请将**security.inter.broker.protocol**设置为PLAINTEXT。

### Oracle CDC客户端

- 我的Oracle CDC客户端管道的数据预览持续超时

  使用Oracle CDC客户端的管道可能需要更长的时间来启动数据预览。如果数据预览超时，请尝试将“预览超时”属性增加到120,000毫秒。

### 销售队伍

- 管道产生缓冲容量错误

  当具有Salesforce起源的管道由于缓冲容量错误（例如）而失败时 `Buffering capacity 1048576 exceeded`，可以通过编辑“订阅”选项卡上的“流缓冲区大小”属性来增加缓冲区大小。

### 脚本起源



- 用户单击“停止”图标时，管道无法停止

  脚本必须包含在用户停止管道时停止脚本的代码。在脚本中，使用该`sdc.isStopped`方法检查管道是否已停止。

- Jython脚本不会超出导入锁定

  如果Jython脚本在失败或错误时未释放导入锁，则管道冻结。如果脚本未释放导入锁，则必须重新启动Data Collector才能释放锁。为避免此问题，请在Jython脚本中使用`try` 带有`finally`块的语句。有关更多信息，请参见[Jython脚本中的线程安全](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/JythonScripting.html#concept_ky4_dmq_q3b)。

### SQL Server CDC客户端

- 具有SQL Server CDC客户端起源的管道无法建立连接。管道失败并出现以下错误：

  `java.sql.SQLTransientConnectionException: HikariPool-3 -    Connection is not available, request timed out after 30004ms. at com.zaxxer.hikari.pool.HikariPool.getConnection(HikariPool.java:213) at com.zaxxer.hikari.pool.HikariPool.getConnection(HikariPool.java:163) at com.zaxxer.hikari.HikariDataSource.getConnection(HikariDataSource.  java:85) at com.streamsets.pipeline.lib.jdbc.multithread.ConnectionManager.  getNewConnection(ConnectionManager.java:45) at com.streamsets.pipeline.lib.jdbc.multithread.ConnectionManager.  getConnection(ConnectionManager.java:57) at com.streamsets.pipeline.stage.origin.jdbc.cdc.sqlserver.  SQLServerCDCSource.getCDCTables(SQLServerCDCSource.java:181)`

  当原始服务器配置为使用一定数量的线程，但线程池设置得不够高时，可能会发生这种情况。在“高级”选项卡上，检查“最大池大小”和“最小空闲连接”属性的设置。

  使用[多线程处理时](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerChange.html#concept_ofh_gns_r1b)，希望将这些属性设置为大于或等于JDBC选项卡上的“线程数”属性的值。

  同样，[允许延迟表处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_nxm_1lp_qbb)要求源服务器使用附加的后台线程。启用处理较晚的表时，应将“最大池大小”和“最小空闲连接”属性设置为一个比“线程数”属性多的线程。

- 删除并重新创建表后，原点似乎无法读取表中的数据。有什么问题？

  SQL Server CDC客户端源存储它处理以跟踪其进度的每个表的偏移量。如果删除表并使用相同的名称重新创建它，则原点将假定它是同一表，并使用该表的最后保存的偏移量。

  如果您需要原点来处理早于最后保存的偏移量的数据，则可能需要[重置原点](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Maintenance/ResettingTheOrigin.html#task_hdg_j1s_5q)。

  请注意，重设原点后，原点会丢弃所有存储的偏移量。并且，当您重新启动管道时，原始服务器将处理指定表中的所有可用数据。您无法重置特定表的原点。

- 预览数据不显示任何值。

  设置“最大事务长度”属性时，起点将在多个时间窗口中获取数据。该属性确定每个时间窗口的大小。预览数据仅显示来自第一个时间窗口的数据，但是原点可能需要先处理多个时间窗口，然后才能找到更改的值以显示在预览中。要在预览数据时查看值，请增加“最大事务长度”或将其设置为-1以在一个时间窗口中获取数据。

- 读取所有更改之前，将生成无数据事件

  设置“最大事务长度”属性时，起点将在多个时间窗口中获取数据。该属性确定每个时间窗口的大小。在处理了每个时间窗口中的所有可用行之后，即使后续的时间窗口仍在处理中，原始数据也会生成no-more-data事件。

## 处理器



使用以下技巧来获得有关处理器的帮助。

### 加密和解密字段

- 启动管道后，日志中显示以下错误消息：

  `CONTAINER_0701 - Stage 'EncryptandDecryptFields_01' initialization error: java.lang.IllegalArgumentException: Input byte array has incorrect ending byte at 44`

  当处理器使用用户提供的密钥时，您提供的Base64编码密钥的长度必须与所选密码套件期望的密钥长度匹配。例如，如果处理器使用264位（32字节）密码套件，则Base64编码的密钥的长度必须为32字节。

  当Base64编码密钥的长度不是预期的长度时，您会收到此消息。

## 目的地

使用以下技巧来获得目标阶段和系统的帮助。

### 卡桑德拉

- 为什么只有少数记录有问题时管道会使整个批次都失败？

  由于Cassandra的要求，当您写入Cassandra集群时，批次是原子的。这意味着一个或多个记录中的错误会导致整个批次失败。

- 为什么我的所有数据都发送给错误？每一批都失败了。

  当每个批次失败时，您可能会遇到数据类型不匹配的情况。Cassandra要求数据的数据类型与Cassandra列的数据类型完全匹配。

  要确定问题，请检查与错误记录关联的错误消息。如果看到类似以下的消息，则说明数据类型不匹配。以下错误消息表明数据类型不匹配是由于Integer数据未成功写入Varchar列：`CASSANDRA_06 - Could not prepare record 'sdk:':  Invalid type for value 0 of CQL type varchar, expecting class java.lang.String but class java.lang.  Integer provided``

  若要更正此问题，您可以使用字段类型转换器处理器转换字段数据类型。在这种情况下，您可以将整数数据转换为字符串。

### Hadoop FS

- 我正在将文本数据写入HDFS。为什么我的文件全部为空？

  您可能没有正确配置管道或Hadoop FS目标。

  Hadoop FS目标使用单个字段将文本数据写入HDFS。

  管道应将所有数据折叠到一个字段中。并且必须将Hadoop FS目标配置为使用该字段。默认情况下，Hadoop FS使用一个名为/ text的字段。

### HBase的

- 验证或启动具有HBase目标的管道时，出现以下错误：

  `HBASE_06 - Cannot connect to cluster: org.apache.hadoop.hbase.MasterNotRunningException:  com.google.protobuf.ServiceException: org.apache.hadoop.hbase.exceptions.ConnectionClosingException:  Call to node00.local/:60000 failed on local exception:  org.apache.hadoop.hbase.exceptions.ConnectionClosingException:  Connection to node00.local/:60000 is closing. Call id=0, waitTime=58`

  您的HBase主服务器正在运行吗？如果是这样，则您可能尝试连接到安全的HBase群集，而不将HBase目标配置为使用Kerberos身份验证。在HBase目标属性中，选择**Kerberos身份验证，**然后重试。

### 卡夫卡制片人

- Kafka Producer可以创建主题吗？

  满足以下所有条件时，Kafka Producer可以创建一个主题：您将Kafka Producer配置为写入不存在的主题名称。为Kafka Producer定义的至少一个Kafka代理已启用auto.create.topics.enable属性。当Kafka Producer查找主题时，具有启用属性的代理将启动并可用。

- 写入Kafka的管道会不断失败并不断循环重启。

  当管道尝试将消息写入比Kafka最大消息大小长的Kafka 0.8时，可能会发生这种情况。

  解决方法：重新配置Kafka代理以允许更大的消息或确保传入记录在配置的限制内。

- 启用Kerberos的Kafka Producer无法连接到Kafka的HDP 2.3发行版。

  启用Kerberos时，默认情况下，HDP 2.3将 **security.inter.broker.protocol** Kafka代理配置属性设置为`PLAINTEXTSASL`，不支持。要更正此问题，请将**security.inter.broker.protocol**设置为PLAINTEXT。

### MemSQL快速加载程序



- 管道停止并返回以下错误：

  `JDBC_14 - Error processing batch. SQLState: 42000 Error Code: 1148 Message: The used command is not allowed with this MySQL version`要将MemSQL快速加载程序与MySQL数据库一起使用，必须在MySQL中启用本地数据加载。参见MySQL主题[LOAD DATA LOCAL的安全性问题](https://dev.mysql.com/doc/refman/8.0/en/load-data-local.html)。

- 管道将CDC记录传递到MemSQL快速加载程序目标，并返回以下错误：

  `JDBC_70 - Unsupported operation in record header: 1`MemSQL快速加载程序目标无法处理CDC记录。使用JDBC Producer目标来处理这些记录。

### SDC RPC

- 管道无法启动，并显示以下验证错误：

  `IPC_DEST_15 Could not connect to any SDC RPC destination : [:  java.net.ConnectException: Connection refused]`

  您配置了管道以将错误记录写入管道，但是错误记录管道的配置信息无效。

  要将错误记录写入管道，您需要一个有效的目标管道，其中应包括RPC起源。

## 执行者

使用以下技巧来帮助执行者。

### 蜂巢查询

- 在阶段中输入Impala JDBC驱动程序的名称时，我收到一条错误消息，指出该驱动程序不在类路径中：

  `HIVE_15 - Hive JDBC Driver  not present in the class path.`

要将Impala JDBC驱动程序与Hive Query执行程序一起使用，该驱动程序必须作为外部库安装。并且必须为Hive Query执行程序使用的舞台库安装它。

如果已经安装了驱动程序，请验证是否已为正确的舞台库安装了该驱动程序。有关更多信息，请参阅[安装Impala驱动程序](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HiveQuery.html#concept_rfq_xk4_nbb)。

## JDBC连接

使用以下技巧来帮助使用JDBC连接来连接数据库的阶段。在某些阶段，Data Collector 包括必要的JDBC驱动程序以连接到数据库。对于其他阶段，必须安装JDBC驱动程序。

以下阶段要求您安装JDBC驱动程序：

- JDBC多表使用者来源
- JDBC查询使用者来源
- MySQL Binary Log的来源
- Oracle CDC客户端起源
- Teradata消费者来源
- JDBC查找处理器
- JDBC Tee处理器
- SQL Parser处理器，在使用数据库解析架构时
- JDBC生产者目标
- MemSQL快速加载程序目标
- JDBC查询执行器

### 没有合适的驱动程序

当Data Collector 找不到某个阶段的JDBC驱动程序时，Data Collector 可能会生成以下错误消息之一：

```
JDBC_00 - Cannot connect to specified database: com.streamsets.pipeline.api.StageException:
JDBC_06 - Failed to initialize connection pool: java.sql.SQLException: No suitable driver
```

确认您已按照说明安装其他驱动程序，如Data Collector 文档中的“ [安装外部库”](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/ExternalLibs.html%23concept_pdv_qlw_ft)中所述。

您还可以使用以下其他技巧来帮助解决问题：

- JDBC连接字符串不正确。

  该阶段的**JDBC连接字符串**属性必须包含`jdbc:`前缀。例如，PostgreSQL连接字符串可能是`jdbc:postgresql:///`。

  检查数据库文档以获取所需的连接字符串格式。例如，如果您使用的是非标准端口，则必须在连接字符串中指定它。

- JDBC驱动程序未存储在正确的目录中。

  您必须将JDBC驱动程序存储在以下目录中：`/streamsets-datacollector-jdbc-lib/lib/`。

  例如，假设您将外部目录定义为 `/opt/sdc-extras`，则将JDBC JAR文件存储在中 `/opt/sdc-extras/streamsets-datacollector-jdbc-lib/lib/`。

- STREAMSETS_LIBRARIES_EXTRA_DIR设置不正确。

  您必须设置`STREAMSETS_LIBRARIES_EXTRA_DIR`环境变量以告知Data Collector JDBC驱动程序和其他附加库的位置。该位置应位于Data Collector安装目录的外部。

  例如，要`/opt/sdc-extras`用作其他库的外部目录，则需要`STREAMSETS_LIBRARIES_EXTRA_DIR`进行如下设置 ：`export STREAMSETS_LIBRARIES_EXTRA_DIR="/opt/sdc-extras/"`

  使用安装类型所需的方法。

- 未设置安全策略。

  您必须授予外部目录中代码的权限。确保 `$SDC_CONF/sdc-security.policy`文件包含以下行：`// user-defined external directory grant codebase "file://-" {  permission java.security.AllPermission; };`

  例如：`// user-defined external directory grant codebase "file:///opt/sdc-extras/-" {  permission java.security.AllPermission; };`

- JDBC驱动程序无法正确加载或注册。

  有时，管道所需的JDBC驱动程序无法正确加载或注册。例如，JDBC驱动程序可能无法正确支持JDBC 4.0自动加载，从而导致出现“没有合适的驱动程序”错误消息。两种方法可以解决此问题：在该阶段的“ **旧版驱动程序”**选项卡上的**“** **JDBC类驱动程序名称”**属性中添加驱动程序的**类名称**。配置数据收集器以自动加载特定的驱动程序。在Data Collector配置文件中`$SDC_CONF/sdc.properties`，取消注释该 `stage.conf_com.streamsets.pipeline.stage.jdbc.drivers.load` 属性，并将其设置为管道中各阶段所需的JDBC驱动程序的逗号分隔列表。

- sdc用户对JDBC驱动程序没有正确的权限。

  当您将Data Collector作为服务运行时，将使用名为的默认系统用户`sdc`来启动服务。用户必须对JDBC驱动程序及其路径中的所有目录具有读取权限。

  要验证权限，请运行以下命令：`sudo -u sdc file /streamsets-datacollector-jdbc-lib/lib/`

  例如，假设您使用的外部目录 `/opt/sdc-extras`和MySQL JDBC驱动程序。如果在运行命令时收到以下输出，则表明该`sdc` 用户对该路径中的一个或多个目录没有读取或执行访问权限：`/opt/sdc-extras/streamsets-datacollector-jdbc-lib/lib/mysql-connector-java-5.1.40-bin.jar: cannot open `/opt/sdc-extras/streamsets-datacollector-jdbc-lib/lib/mysql-connector-java-5.1.40-bin.jar' (Permission denied)`

  要解决此问题，请标识相关目录并授予 `sdc`用户读取和执行这些目录上的访问权限。例如，运行以下命令来授予用户对外部目录根目录的访问权限：`chmod 755 /opt/sdc-extras`

  如果在运行命令时收到以下输出，则该 `sdc`用户对JDBC驱动程序没有读取权限：`/opt/sdc-extras/streamsets-datacollector-jdbc-lib/lib/mysql-connector-java-5.1.40-bin.jar: regular file, no read permission`

  要解决此问题，请运行以下命令以授予用户对驱动程序的读取访问权限：`chmod 644 /opt/sdc-extras/streamsets-datacollector-jdbc-lib/lib/mysql-connector-java-5.1.40-bin.jar`

### 无法连接到数据库

当Data Collector无法连接到数据库时，将显示以下错误消息-确切的消息可能会因驱动程序而异：

```
JDBC_00 - Cannot connect to specified database: com.zaxxer.hikari.pool.PoolInitializationException:
Exception during pool initialization: The TCP/IP connection to the host 1.2.3.4, port 1234 has failed
```

在这种情况下，请验证Data Collector 计算机可以在相关端口上访问数据库计算机。为此，您可以使用诸如ping和netcat（nc）之类的工具。例如，要验证主机1.2.3.4是否可访问：

```
$ ping 1.2.3.4 
PING 1.2.3.4 (1.2.3.4): 56 data bytes 
64 bytes from 1.2.3.4: icmp_seq=0 ttl=57 time=12.063 ms 
64 bytes from 1.2.3.4: icmp_seq=1 ttl=57 time=11.356 ms 
64 bytes from 1.2.3.4: icmp_seq=2 ttl=57 time=11.626 ms 
^C
--- 1.2.3.4 ping statistics --- 
3 packets transmitted, 3 packets received, 0.0% packet loss 
round-trip min/avg/max/stddev = 11.356/11.682/12.063/0.291 ms
```

然后验证是否可以访问端口1234：

```
$ nc -v -z -w2 1.2.3.4 1234 
nc: connectx to 1.2.3.4 port 1234 (tcp) failed: Connection refused
```

如果主机或端口不可访问，请检查路由和防火墙配置。

### MySQL JDBC驱动程序和时间值

由于MySQL JDBC驱动程序问题，驱动程序无法将时间值返回毫秒。而是，驱动程序将值返回到第二个。

例如，如果列的值为20：12：50.581，则驱动程序将读取的值为20：12：50.000。

## 性能

使用以下技巧来获得性能方面的帮助：

- 当为更大的批次配置来源时，为什么我的批次大小只有1000条记录？

  该数据收集器 的最大批量大小覆盖在起源配置的最大批量大小。该数据采集器默认是1000条记录。

  配置原始批处理大小时，可以请求最大Data Collector的最大批处理大小，或者可以增加Data Collector 配置文件中的**production.maxBatchSize**属性 。`$SDC_CONF/sdc.properties`

- 如何减少从原始系统读取之间的延迟？

  当管道读取记录的速度快于处理记录或将记录写入目标系统的速度时，从源系统读取之间可能会出现较长的延迟。因为管道一次处理一个批次，所以管道必须等到将一个批次提交到目标系统后再读取下一个批次，以防止管道以稳定的速率读取数据。稳定地读取数据要比偶尔读取提供更好的性能。

  如果无法增加处理器或目标的吞吐量，请限制管道从原始系统读取记录的速率。配置管道的“ **速率限制”**属性，以定义管道在一秒钟内可以读取的最大记录数。

- 当我尝试启动一个或多个管道时，收到一个错误，提示没有足够的线程可用。

  默认情况下，Data Collector可以同时运行大约22个独立管道。如果您同时运行大量独立管道，则可能会收到以下错误消息：`CONTAINER_0166 - Cannot start pipeline '' as there are not enough threads available`

  要解决此错误，请增加Data Collector 配置文件中的**Runner.thread.pool.size** 属性的值。`$SDC_CONF/sdc.properties`

  有关更多信息，请参阅Data Collector文档中的“ [运行多个并发管道](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23task_nkc_ydl_bw) ” 。

- 如何改善总体管道性能？

  您可以通过调整管道使用的批处理大小来提高性能。批处理大小决定一次有多少数据通过管道。默认情况下，批处理大小为1000条记录。

  您可以根据记录的大小或它们到达的速度来调整批处理大小。例如，如果您的记录非常大，则可以减小批大小以提高处理速度。或者，如果记录很小并且很快到达，则可以增加批处理的大小。

  尝试批量大小并查看结果。

  要更改批处理大小，请在Data Collector 配置文件中配置**production.maxBatchSize**属性。`$SDC_CONF/sdc.properties`

## 集群执行模式

使用以下技巧来帮助集群模式下的管道：

- 配置集群管道时出现以下验证错误。这是什么意思？

  `Validation_0071 - Stage '' does not support 'Standalone' execution mode`

  当您在群集管道中包含非群集源时，可能会出现此消息。您可以在集群管道中使用Kafka Consumer的集群版本和Hadoop FS起源。

  如果您选择“写入文件”选项以处理管道错误，也会出现该消息。群集模式不支持“写入文件”。

- 为什么Data Collector不 从我的新Kafka分区读取数据？

  如果在Kafka主题中创建新分区，要启动新的Data Collector 工作程序以从该分区读取数据，则需要重新启动管道。

- 我的管道无法启动，并出现以下错误：

  `Pipeline Status: START_ERROR: Unexpected error starting pipeline:java.lang.IllegalStateException:  Timed out after waiting 121 seconds for cluster application to start. Submit command is not alive.`检查数据收集器 日志以获取更多信息。YARN上的Spark客户端配置可能不正确，安装已过期或使用的节点不是网关节点。

- 我的管道无法启动，并出现以下错误：

  `Pipeline Status: START_ERROR: IO Error while trying to start the pipeline: java.io.IOException:  Kerberos Error: No valid credentials provided (Mechanism level: Failed to find any Kerberos tgt)]`

  群集可能配置为使用Kerberos，但数据收集器未配置为使用Kerberos。有关为Data Collector启用Kerberos身份验证的详细信息，请参阅Data Collector文档中的[Kerberos身份验证](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_hnm_n4l_xs)。

- 我的管道意外停止。

  检查YARN Resource Manager UI中的Spark Application Master日志以获取有关该问题的更多信息。

- 为什么我的管道启动需要这么长时间？

  管道的开始时间可以根据YARN群集的繁忙程度而有所不同。通常，群集管道应在30-90秒内启动。