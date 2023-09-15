# MapReduce执行器

每次接收到事件记录时，MapReduce执行程序都会在HDFS或MapR FS中启动MapReduce作业。将MapReduce执行程序用作事件流的一部分。

您可以使用MapReduce执行程序来启动自定义作业，例如用于比较文件中记录数的验证作业。您可以通过在执行程序中配置自定义作业或使用预定义的配置对象来使用它。您还可以使用MapReduce执行程序来启动预定义的作业。MapReduce执行程序包括两个预定义的作业：一个将Avro文件转换为ORC文件，另一个将Avro文件转换为Parquet。

您可以以任何逻辑方式使用执行程序，例如在Hadoop FS或MapR FS目标关闭文件后运行MapReduce作业。例如，您可以在MapR FS目标关闭文件后，使用Avro到ORC作业将Avro文件转换为ORC文件。或者，在Hadoop FS目标关闭[Hive漂移同步解决方案的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Hive_Drift_Solution/HiveDriftSolution_title.html#concept_phk_bdf_2w)一部分后，您可以使用Avro to Parquet作业将Avro文件转换为Parquet 。

**注意：** MapReduce执行程序在外部系统中启动作业。它不会监视作业或等待作业完成。成功执行作业后，执行者即可进行其他处理。

配置MapReduce执行程序时，可以指定连接信息和作业详细信息。对于预定义的作业，您可以指定Avro转换详细信息，例如输入和输出文件的位置，以及特定于ORC或Parquet的详细信息。对于其他类型的作业，您可以指定作业创建者或配置对象，以及要使用的作业配置属性。

必要时，您可以启用Kerberos身份验证并指定MapReduce用户。您还可以使用MapReduce配置文件，并根据需要添加其他MapReduce配置属性。

您还可以配置执行程序以为另一个事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

有关使用MapReduce执行程序的[案例研究](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_jkm_rnz_kx)，请参阅[案例研究：Parquet转换](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_jkm_rnz_kx)。

## 先决条件

在运行包含MapReduce执行程序的管道之前，必须启用MapReduce执行程序才能提交作业。

您可以启用MapReduce执行程序以几种不同的方式提交作业。执行 以下任务之一，以使执行者可以提交作业：

- 配置YARN最低用户ID属性min.user.id

  min.user.id属性默认设置为1000。要允许提交工作：验证数据收集器用户正在使用的用户ID ，通常称为“ sdc”。在Hadoop中，配置YARN min.user.id属性。将该属性设置为等于或小于 Data Collector用户ID。

- 配置YARN允许的系统用户属性allowed.system.users

  allowed.system.users属性列出了允许的用户名。要允许提交工作：在Hadoop中，配置YARN allowed.system.users属性。将Data Collector用户名（通常为“ sdc”）添加到允许的用户列表中。

- 配置MapReduce执行程序MapReduce用户属性

  在MapReduce执行程序中，MapReduce用户属性允许您输入提交作业时要使用的阶段的用户名。要允许提交工作：在MapReduce执行程序阶段，配置MapReduce用户属性。输入一个ID高于min.user.id属性的用户，或者输入一个在allowed.system.users属性中列出的用户名。

  有关MapReduce用户属性的信息，请参阅《[使用MapReduce用户》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/MapReduce.html#concept_akp_p3m_zx)。

## 相关事件产生阶段

在管道的事件流中使用MapReduce执行程序。MapReduce执行程序旨在在写入输出文件后启动MapReduce作业。

使用MapReduce执行程序对由以下目标写入的文件执行后处理：

- Hadoop FS目标
- MapR FS目的地

## MapReduce作业和作业配置属性

MapReduce执行程序可以运行必须配置的自定义作业，也可以运行该执行程序提供的预定义作业。

配置自定义作业时，可以指定作业创建者和作业配置属性，也可以使用自定义配置对象并指定作业配置属性。

使用预定义的作业时，您可以指定作业，Avro转换详细信息以及与作业相关的属性。

配置作业配置属性时，可以指定键值对。您可以在键值对中使用表达式。

### 实木复合地板和ORC的预定义作业

MapReduce执行程序包括两个预定义的作业：Avro到ORC和Avro到Parquet。

Avro到ORC作业将Avro文件转换为ORC文件。Avro到Parquet作业将Avro文件转换为Parquet。写入后，两个作业都会处理Avro文件。即，目的地完成了Avro文件的写入并生成了事件记录。事件记录包含有关文件的信息，包括文件的名称和位置。当MapReduce执行程序收到事件记录时，它将启动所选的预定义MapReduce作业。

使用预定义作业时，可以配置输入文件信息和输出目录，是否保留输入文件以及是否覆盖临时文件。

默认情况下，对于输入文件，MapReduce执行程序使用事件记录的“文件路径”字段中的文件名和位置，如下所示：

```
${record:value('/filepath')}
```

执行程序将输出文件写入指定的输出目录。执行程序使用已处理的输入文件名作为输出文件名的基础，并根据作业类型添加 `.parquet`或`.orc`。

使用“ Avro到ORC”作业时，可以在“ Avro到ORC”选项卡上指定ORC批处理大小。要指定其他作业信息，请在“作业”选项卡上添加作业配置属性。有关您可能要使用的属性的信息，请参见[Hive文档](https://orc.apache.org/docs/hive-config.html)。

使用“从Avro到Parquet”作业时，可以在“从Avro到Parquet”选项卡上指定特定于作业的属性。您可以通过在“作业”选项卡上添加作业配置属性来指定其他作业信息。

## 事件产生

MapReduce执行程序可以生成可在事件流中使用的事件。启用事件生成后，执行程序每次启动MapReduce作业时都会生成事件。

MapReduce执行程序事件可以任何逻辑方式使用。例如：

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

由MapReduce执行程序生成的事件记录具有以下与事件相关的记录头属性。记录标题属性存储为字符串值：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下类型之一：作业创建-执行者创建并启动MapReduce作业时生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

MapReduce执行程序生成的事件记录具有以下字段：

| 活动栏位名称 | 描述                     |
| :----------- | :----------------------- |
| 追踪网址     | MapReduce作业的跟踪URL。 |
| 工作编号     | MapReduce作业的作业ID。  |

## Kerberos身份验证

您可以使用Kerberos身份验证连接到Hadoop服务，例如HDFS或YARN。使用Kerberos身份验证时，Data Collector 使用Kerberos主体和keytab进行身份验证。默认情况下，Data Collector 使用启动它的用户帐户进行连接。

Kerberos主体和密钥表在Data Collector 配置文件中定义`$SDC_CONF/sdc.properties`。要使用Kerberos身份验证，请在数据收集器 配置文件中配置所有Kerberos属性，然后在MapReduce执行程序中启用Kerberos。

有关为Data Collector启用Kerberos身份验证的详细信息，请参阅Data Collector文档中的[Kerberos身份验证](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_hnm_n4l_xs)。

## 使用MapReduce用户

Data Collector 可以使用当前登录的Data Collector用户或在 执行程序中配置的用户来提交作业。

可以设置需要使用当前登录的Data Collector用户的Data Collector配置属性 。如果未设置此属性，则可以在源中指定一个用户。有关Hadoop模拟和Data Collector属性的更多信息，请参阅Data Collector文档中的[Hadoop Impersonation Mode](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_pmr_sy5_nz)。

请注意，执行程序使用其他用户帐户进行连接。默认情况下，Data Collector使用启动它的用户帐户连接到外部系统。使用Kerberos时，Data Collector使用Kerberos主体。

要在执行程序中配置用户，请执行以下任务：

1. 在外部系统上，将用户配置为代理用户，并授权该用户模拟MapReduce用户。

   有关更多信息，请参见MapReduce文档。

2. 在MapReduce执行程序的**MapReduce**选项卡上，配置**MapReduce用户**属性。

## 配置一个MapReduce执行器

配置一个MapReduce执行程序，使其在每次执行程序收到事件记录时启动MapReduce作业。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 您要使用的库版本。                                           |
   | 产生事件 [![img](imgs/icon_moreInfo-20200310203437598.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/MapReduce.html#concept_e1s_sm5_sx) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击**添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |

2. 在“ **MapReduce”**选项卡上，配置以下属性：

   | MapReduce属性                                                | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | MapReduce配置目录                                            | 包含Hive和Hadoop配置文件的目录的绝对路径。对于Cloudera Manager安装，请输入hive-conf。该阶段使用以下配置文件：core-site.xmlyarn-site.xmlmapred-site.xml**注意：**配置文件中的属性被此阶段定义的单个属性覆盖。 |
   | MapReduce配置                                                | 要使用的其他属性。要添加属性，请单击**添加**并定义属性名称和值。使用HDFS或MapR FS期望的属性名称和值。 |
   | MapReduce用户[![img](imgs/icon_moreInfo-20200310203437598.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/MapReduce.html#concept_akp_p3m_zx) | 用于连接到外部系统的MapReduce用户。使用此属性时，请确保正确配置了外部系统。未配置时，管道将使用当前登录的Data Collector用户。将Data Collector配置为使用当前登录的Data Collector用户时，不可配置。有关更多信息，请参阅Data Collector 文档 中的[Hadoop模拟模式](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_pmr_sy5_nz)。 |
   | Kerberos身份验证[![img](imgs/icon_moreInfo-20200310203437598.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/MapReduce.html#concept_kry_p3y_mx) | 使用Kerberos凭据连接到外部系统。选中后，将使用Data Collector配置文件中 定义的Kerberos主体和密钥表`$SDC_CONF/sdc.properties`。 |

3. 在“ **作业”**选项卡上，配置以下属性：

   | 工作性质                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 职务名称                                                     | MapReduce作业的显示名称。此名称显示在Hadoop Web应用程序和列出MapReduce作业的其他报告工具中。 |
   | 工作类型 [[![img](imgs/icon_moreInfo-20200310203437598.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/MapReduce.html#concept_jqk_g4y_mx) | 要运行的MapReduce作业类型：自定义-使用自定义作业创建者界面和作业配置属性来定义作业。配置对象-使用配置对象和作业配置属性来定义作业。将Avro转换为Parquet-使用预定义的作业将Avro文件转换为Parquet。指定Avro转换属性，并可以选择配置作业的其他作业配置属性。将Avro转换为ORC-使用预定义的作业将Avro文件转换为ORC文件。指定Avro转换属性，并可以选择配置作业的其他作业配置属性。 |
   | 自定义JobCreator                                             | MapReduce Job Creator界面，用于自定义作业。                  |
   | 作业配置                                                     | 配置属性的键/值对，用于定义作业。您可以在键和值中使用表达式。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以添加其他属性。 |

4. 使用预定义的作业时，单击“ **Avro转换”** 选项卡，然后配置以下属性：

   | Avro转换属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 输入Avro文件[![img](imgs/icon_moreInfo-20200310203437598.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/MapReduce.html#concept_cnx_hqy_mx) | 该表达式的计算结果为要处理的Avro文件的名称和位置。默认情况下，使用事件记录的filepath字段中指定的名称和位置处理文件。 |
   | 保留输入文件                                                 | 将处理后的Avro文件保留在原位。默认情况下，执行程序将在处理后删除文件。 |
   | 输出目录                                                     | 写入生成的ORC或Parquet文件的位置。使用绝对路径。             |
   | 覆盖临时文件                                                 | 启用该选项以覆盖先前作业运行后剩余的所有现有临时文件。       |

5. 要使用从Avro到Parquet的作业，请单击**Avro到Parquet的** 选项卡，然后配置以下属性：

   | 阿夫罗到镶木地板属性 | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | 压缩编解码器         | 要使用的压缩编解码器。如果您不输入压缩代码，则执行程序对Parquet使用默认的压缩编解码器。 |
   | 行组大小             | 实木复合地板行组大小。使用-1以使用Parquet默认值。            |
   | 页面大小             | 实木复合地板页面大小。使用-1以使用Parquet默认值。            |
   | 词典页面大小         | 实木复合地板字典页面大小。使用-1以使用Parquet默认值。        |
   | 最大填充尺寸         | 实木复合地板的最大填充尺寸。使用-1以使用Parquet默认值。      |

6. 要使用“ Avro到ORC”作业，请单击“ **Avro到ORC”**选项卡，然后配置以下属性：

   | Avro转换为ORC属性 | 描述                          |
   | :---------------- | :---------------------------- |
   | ORC批次大小       | 一次写入ORC文件的最大记录数。 |