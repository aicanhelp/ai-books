# spark执行器

每次执行事件收到时，Spark执行程序都会启动一个Spark应用程序。您可以将Spark执行程序与YARN上的Spark一起使用。执行器目前与Mesos上的Spark不兼容。

使用Spark执行程序作为事件流的一部分启动Spark应用程序。您可以以任何逻辑方式使用执行程序，例如在Hadoop FS，MapR FS或Amazon S3目标关闭文件后运行Spark应用程序。例如，您可以使用执行程序来启动Spark应用程序，每次Hadoop FS目标关闭文件时，该应用程序会将Avro文件转换为Parquet。

请注意，Spark执行程序在外部系统中启动应用程序。它不会监视应用程序或等待它完成。成功执行申请后，执行者即可进行其他处理。

Spark执行程序可以在客户端或集群模式下运行应用程序。仅在不考虑资源使用的情况下，才能在客户端模式下运行该应用程序。

在使用Spark执行程序之前，请确保执行必备任务。

配置Spark执行程序时，可以指定Spark应该使用的工作程序节点数，也可以启用动态分配并指定工作程序节点的最小和最大数目。动态分配使Spark可以根据需要在指定范围内使用其他工作程序节点。

您可以指定其他群集管理器属性以传递给Spark，例如应用程序驱动程序和执行程序可以使用的最大内存量。

您还可以配置其他Spark参数和环境变量。您输入的任何参数和变量都会覆盖之前的所有定义，包括Spark应用程序中，Spark执行程序中其他位置以及Data Collector 计算机中的定义。

您可以指定自定义Spark和Java主目录以及Hadoop代理用户。如果需要，还可以输入Kerberos凭据。

配置应用程序详细信息时，可以指定用于编写应用程序的语言，然后定义特定于语言的属性。

您还可以配置执行程序以为另一个事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

## Spark版本和舞台库

Spark执行程序仅支持Spark 2.1或更高版本。

使用Spark执行程序时，请确保所有相关组件的Spark版本均相同，如下所示：

- 使用执行程序在YARN上的Spark上运行应用程序时，请确保

  所选阶段库中使用的Spark版本与用于构建应用程序的Spark版本匹配。

  例如，如果使用Spark 2.1构建应用程序，请使用Spark 2.1阶段库之一中提供的Spark执行程序。

- 在群集流传输管道中使用执行程序时，所选阶段库中的Spark版本也必须与群集使用的Spark版本匹配。

  例如，如果您的集群使用Spark 2.2，请使用包含Spark 2.2的阶段库。

Spark执行器可在多个CDH和MapR阶段库中找到。要验证舞台库包含的Spark版本，请参阅CDH或MapR文档。有关包含Spark Evaluator的阶段库的更多信息，请参阅Data Collector 文档 中的[Available Stage Libraries](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Installation/AddtionalStageLibs.html%23concept_evs_xkm_s5)。

## 先决条件

在运行在YARN上启动应用程序的Spark执行程序管道之前，必须启用Spark执行程序才能提交应用程序。

您可以启用Spark执行程序以几种不同的方式提交应用程序。执行

一个

下列任务，以使执行人提出申请：

- 配置YARN最低用户ID属性min.user.id

  min.user.id属性默认设置为1000。要允许提交工作：验证数据收集器用户正在使用的用户ID ，通常称为“ sdc”。在Hadoop中，配置YARN min.user.id属性。将该属性设置为等于或小于 Data Collector用户ID。

- 配置YARN允许的系统用户属性allowed.system.users

  allowed.system.users属性列出了允许的用户名。要允许提交工作：在Hadoop中，配置YARN allowed.system.users属性。将Data Collector用户名（通常为“ sdc”）添加到允许的用户列表中。

- 配置Spark执行程序Proxy User属性

  在Spark执行器中，“代理用户”属性允许您输入阶段名称，以便在提交应用程序时使用。允许提交申请：在Spark执行程序阶段，在**Spark**选项卡上，配置**Proxy User**属性。输入一个ID高于min.user.id属性的用户，或者输入一个在allowed.system.users属性中列出的用户名。

  有关使用Hadoop用户的信息，请参阅《[使用代理Hadoop用户》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Spark.html#concept_twh_wsg_gz)。

## Spark Home需求

在YARN上运行应用程序时，Spark执行程序需要访问位于Spark安装目录中的spark-submit脚本。

默认情况下，Spark执行程序使用Data Collector 计算机上SPARK_HOME环境变量中定义的目录。在启动Data Collector之前，必须设置SPARK_HOME环境变量。

**注意：**在Cloudera CDH集群上安装Spark 2时，请为Data Collector设置SPARK_HOME环境变量， 如下所示：

```
export SPARK_HOME=/opt/cloudera/parcels/SPARK2/lib/spark2
```

您可以根据需要通过在executor阶段属性中配置Custom Spark Home属性来覆盖环境变量。当未设置SPARK_HOME环境变量或指向冲突版本的Spark时，请使用Custom Spark Home属性。

例如，如果您将Spark 2.1阶段库用于Spark执行程序，并且SPARK_HOME指向Spark的早期版本，请使用Custom Spark Home属性指定Spark 2.1 spark-submit脚本的位置。

## 应用属性

使用Spark执行程序时，您可以指定应用程序名称。应用程序名称显示在集群管理器和Spark服务器日志中，因此请使用唯一名称来区分Spark应用程序和其他应用程序。例如，SDC_ <管道名称> _ <app_type>。

在执行程序中，您可以启用详细日志记录以帮助测试管道和调试应用程序。

根据用于编写应用程序的语言配置其他应用程序详细信息：

- Java或Scala

  对于用Java或Scala编写的应用程序，请指定主类和应用程序资源-主JAR或文件的完整路径。

  您可以指定其他参数和要使用的JAR。您还可以使用`--files` 协议将其他文件传递给应用程序。

- 蟒蛇

  对于用Python编写的应用程序，您可以指定应用程序资源-主Python文件的完整路径-以及任何必需的依赖项。您可以定义应用程序参数，并使用`--files`协议将其他文件传递给应用程序。

**注意：**确保运行Data Collector的用户-或Hadoop代理用户（如果已配置）-对所有必需路径具有读取权限。

## 使用代理Hadoop用户

您可以将Spark执行程序配置为使用Hadoop用户作为代理用户，以将应用程序提交到YARN上的Spark。

默认情况下，Data Collector 使用启动它的用户帐户连接到外部系统。使用Kerberos时，数据收集器 可以使用在执行程序中指定的Kerberos主体。

要使用Hadoop用户，请执行以下任务：

1. 在外部系统上，将

   Data Collector

   用户配置为代理用户，并授权

   Data Collector

   用户模拟Hadoop用户。

   有关更多信息，请参阅Hadoop文档。

2. 在Spark执行程序的**Spark**选项卡上，将**Proxy User**属性配置 为使用Hadoop用户名。

## Kerberos身份验证

您可以使用Kerberos身份验证连接到写入输出文件的目标系统。要启用此功能，请在Spark执行程序的“凭据”选项卡上，输入运行应用程序的YARN群集的Kerberos主体和密钥选项卡。

## 事件产生

Spark执行程序可以生成可在事件流中使用的事件。启用事件生成后，执行程序每次启动Spark应用程序时都会生成事件。

Spark执行程序事件可以任何逻辑方式使用。例如：

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

由于Spark执行程序事件包括启动的每个应用程序的应用程序ID，因此您可能会生成事件以记录该应用程序ID。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

Spark执行程序生成的事件记录具有以下与事件相关的记录头属性。记录标题属性存储为字符串值：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下类型：AppSubmittedEvent-在执行程序启动Spark应用程序时生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

Spark执行程序生成的事件记录具有以下字段：

| 活动栏位名称 | 描述                            |
| :----------- | :------------------------------ |
| app_id       | Spark应用程序的YARN应用程序ID。 |

## 监控方式

Data Collector 不监视Spark应用程序。使用常规群集监视器应用程序查看应用程序的状态。

由Spark执行程序启动的应用程序使用阶段中指定的应用程序名称显示。该应用程序的所有实例的应用程序名称均相同。您可以在Data Collector 日志中找到特定实例的应用程序ID 。

Spark执行程序还将应用程序ID写入事件记录。要保留所有应用程序ID的记录，请启用该阶段的事件生成。

## 配置Spark执行器

配置Spark执行程序以在每次执行程序收到事件记录时启动Spark应用程序。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 您要使用的库版本。                                           |
   | 产生事件 [![img](imgs/icon_moreInfo-20200310203628756.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Spark.html#concept_xmx_1wg_gz) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击**添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |

2. 在“ **Spark”**选项卡上，配置以下属性：

   | 星火地产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 部署模式                                                     | 应用程序的部署方式：客户端-以Spark客户端模式运行应用程序。仅在不关心资源时使用。群集-以Spark群集模式运行应用程序。群集模式将应用程序部署在YARN群集上。 |
   | 驱动程序内存                                                 | 驱动程序可以为应用程序使用的最大内存量。输入数字和标准Java度量单位，不带空格。例如10m。您可以使用k或K，m或M或g或G。 |
   | 执行者记忆                                                   | 执行程序可以使用的最大内存量。输入数字和标准Java度量单位，不带空格。例如，100k。您可以使用k或K，m或M或g或G。 |
   | 动态分配                                                     | 启用执行程序的动态分配以启动应用程序。                       |
   | 工作节点数                                                   | Spark要使用的工作程序节点的确切数量。不使用动态分配时进行配置。 |
   | 最小工作节点数                                               | Spark要使用的最小工作节点数。使用动态分配时进行配置。        |
   | 最大工作节点数                                               | Spark要使用的最大工作节点数。使用动态分配时进行配置。        |
   | 代理用户[![img](imgs/icon_moreInfo-20200310203628756.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Spark.html#concept_twh_wsg_gz) | Hadoop用户连接到外部系统并运行该应用程序。使用此属性时，请确保正确配置了外部系统。默认情况下，管道使用Data Collector用户。 |
   | 自定义Spark Home [![img](imgs/icon_moreInfo-20200310203628756.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Spark.html#concept_aht_p4h_c1b) | 用于输入自定义Spark主目录。默认情况下，源使用Data Collector计算机上SPARK_HOME环境变量中指定的目录。此属性将覆盖SPARK_HOME环境变量。如果未为Data Collector计算机设置环境变量，或者为错误版本的Spark设置了变量，则为必需。例如，要针对Spark 2.1运行作业，如果SPARK_HOME环境变量指向Spark的早期版本，则将此属性指向Spark 2.1目录。 |
   | 自定义Java主页                                               | 用于输入自定义Java主目录。默认情况下，源使用Data Collector计算机上JAVA_HOME环境变量中指定的目录。此属性将覆盖Data Collector环境变量。如果未为Data Collector计算机设置环境变量，则为必需。 |
   | 其他Spark参数                                                | 传递给Spark的其他参数。覆盖指定参数的所有先前配置。有关可用参数的列表，请参见Spark文档。 |
   | 其他Spark参数和值                                            | 具有值的其他参数将传递给Spark。覆盖指定参数的所有先前配置。有关可用参数的列表，请参见Spark文档。 |
   | 环境变量                                                     | 要使用的其他环境变量。覆盖指定参数的所有先前配置。有关有效环境变量的列表，请参见Spark文档。 |

3. 单击“ **应用程序”**选项卡，选择用于编写应用程序的 **语言**，然后配置以下属性：

   对于用Java或Scala编写的应用程序，请配置以下属性：

   | Java / Scala应用程序属性 | 描述                                                         |
   | :----------------------- | :----------------------------------------------------------- |
   | 应用名称                 | 在YARN资源管理器和日志中显示的名称。还显示在Spark服务器历史记录页面中。**提示：**请使用区分应用程序和其他进程和其他管道启动的名称的名称，例如SDC_ <管道名称> _ <app_type>。 |
   | 应用资源                 | 包含主类的JAR的完整路径。                                    |
   | 主班                     | Spark应用程序的主类的完整路径。                              |
   | 应用参数                 | 您可以添加其他参数以传递给应用程序。完全按预期的顺序输入参数。执行程序不验证参数。 |
   | 其他JAR                  | 您可以指定要使用的其他JAR。输入JAR的完整路径。               |
   | 其他档案                 | 使用`--files`协议将其他文件传递给应用程序 。输入文件的完整路径。有关协议的信息，请参见Spark文档。 |
   | 启用详细日志记录         | 启用将其他信息记录到Data Collector日志中。为避免用不必要的信息填充日志，请仅在测试管道时启用此属性。 |

   对于用Python编写的应用程序，请配置以下属性：

   | Python应用程序属性 | 描述                                                         |
   | :----------------- | :----------------------------------------------------------- |
   | 应用名称           | 在YARN资源管理器和日志中显示的名称。还显示在Spark服务器历史记录页面中。**提示：**请使用区分应用程序和其他进程和其他管道启动的名称的名称，例如SDC_ <管道名称> _ <app_type>。 |
   | 应用资源           | 要运行的Python文件的完整路径。                               |
   | 应用参数           | 您可以添加其他参数以传递给应用程序。完全按预期的顺序输入参数。执行程序不验证参数。 |
   | 依存关系           | Python应用程序资源所需的任何文件的完整路径。                 |
   | 其他档案           | 使用`--files`协议将其他文件传递给应用程序 。输入文件的完整路径。有关协议的信息，请参见Spark文档。 |
   | 启用详细日志记录   | 启用将其他信息记录到Data Collector日志中。为避免用不必要的信息填充日志，请仅在测试管道时启用此属性。 |

4. （可选）单击“ **凭据”**选项卡并配置以下属性：

   | 凭证属性[![img](imgs/icon_moreInfo-20200310203628756.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Spark.html#concept_bhk_fhg_gz) | 描述                                     |
   | :----------------------------------------------------------- | :--------------------------------------- |
   | Kerberos主体                                                 | 运行应用程序的YARN群集的Kerberos主体。   |
   | Kerberos密钥表                                               | 运行应用程序的YARN群集的Kerberos密钥表。 |