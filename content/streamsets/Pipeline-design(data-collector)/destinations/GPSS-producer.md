# GPSS制作人

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310184322757.png) 资料收集器

GPSS Producer目标通过Greenplum流服务器（GPSS）将数据写入Greenplum数据库。

在配置GPSS Producer目标时，您可以为Greenplum数据库主服务器和Greenplum流服务器指定连接信息，定义要使用的表，并可以选择定义字段映射。默认情况下，目标将字段数据写入具有匹配名称的列。

GPSS Producer目标可以使用在`sdc.operation.type`记录头属性中定义的CRUD操作 来写入数据。您可以为没有标题属性或值的记录定义默认操作。您还可以配置如何处理不受支持的操作的记录。 有关Data Collector更改数据处理以及启用CDC的来源的列表的信息，请参见[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

在使用GPSS Producer目标之前，必须安装GPSS舞台库并完成其他[先决任务](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/GPSS.html#concept_axh_wlz_q3b)。GPSS 阶段库是一个[Enterprise阶段库](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Installation/EnterpriseStageLibraries.html#concept_s1r_1gg_dhb)，仅供开发用途免费。有关购买用于生产的舞台库的信息，请[联系StreamSets](https://streamsets.com/contact-us/)。

## 先决条件



使用GPSS Producer目标之前，请完成以下先决条件：

- [安装GPSS舞台库](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MemSQLLoader.html#concept_q2c_chg_kgb)。
- [在Greenplum数据库中安装，配置和启动GPSS](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/GPSS.html#concept_utj_jjq_s3b)。



### 安装GPSS舞台库

您必须先安装GPSS舞台库，然后才能使用GPSS Producer目标。

GPSS 阶段库是一个Enterprise阶段库，仅供开发用途免费。有关购买用于生产的舞台库的信息，请[联系StreamSets](https://streamsets.com/contact-us/)。

您可以使用Package Manager来安装Enterprise阶段库以进行tarball Data Collector的安装，也可以将其作为定制阶段库来进行tarball，RPM或Cloudera Manager Data Collector的 安装。

#### 支持的版本

下表列出了与特定的Data Collector 版本一起使用的GPSS Enterprise阶段库的版本：

| 数据收集器版本             | 支持的舞台库版本 |
| :------------------------- | :--------------- |
| 数据收集器 3.8.2及更高版本 | GPSS企业库1.0.0  |

#### 使用软件包管理器安装

您可以使用软件包管理器在tarball Data Collector 安装中安装GPSS阶段库。

1. 单击“程序包管理器”图标：![img](imgs/icon_PackageManager-20200310184323748.png)。

2. 在导航面板中，单击**Enterprise Stage Libraries**。

3. 选择**GPSS企业库**，然后单击 **安装**图标：![img](imgs/icon_InstallLib-20200310184324050.png)。

4. 阅读StreamSets 订阅服务条款。如果您同意，请选中复选框，然后单击“ **安装”**。

   Data Collector将安装所选的舞台库。

5. 重新启动Data Collector。

#### 作为自定义舞台库安装

您可以将GPSS Enterprise阶段库作为自定义阶段库安装在tarball，RPM或Cloudera Manager Data Collector 安装上。

1. 要下载舞台库，请转到[StreamSets下载企业连接器](https://streamsets.com/download/enterprise-connectors/)页面。

   该网页显示按发布日期组织的Enterprise阶段库，并在页面顶部显示最新版本。

2. 单击您要下载的Enterprise阶段库名称和版本。

3. 在“ **下载企业连接器”**表单中，输入您的姓名和联系信息。

4. 阅读StreamSets订阅服务条款。如果您同意，请接受服务条款，然后单击“ **提交”**。

   舞台库下载。

5. 将Enterprise阶段库安装和管理为自定义阶段库。

   有关更多信息，请参见[Custom Stage Libraries](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/CustomStageLibraries.html#concept_pmc_jk1_1x)。

### 在Greenplum数据库中安装，配置和启动GPSS

Greenplum流服务器（GPSS）管理GPSS生产者目的地和Greenplum数据库之间的通信和数据传输。使用目标之前，必须在Greenplum数据库群集中安装，配置和启动GPSS。有关更多信息，请参见[Pivotol Greenplum文档](https://gpdb.docs.pivotal.io/5160/greenplum-stream/instcfgmgt.html)。

## 定义CRUD操作

GPSS Producer目标可以插入，更新或合并数据。目标根据CRUD操作标头属性或与操作相关的阶段属性中定义的CRUD操作写入记录。

您可以通过以下方式定义CRUD操作：

- CRUD记录标题属性

  您可以在CRUD操作记录标题属性中定义CRUD操作。目标在`sdc.operation.type`记录头属性中寻找要使用的CRUD操作 。

  该属性可以包含以下数值之一：INSERT为13更新8合并

  如果您的管道包括启用CRUD的原始数据源，该原始数据元处理已更改的数据，则目标位置仅从`sdc.operation.type`原始数据源生成的标头属性中读取操作类型 。如果管道使用非CDC来源，则可以使用表达式评估器或脚本处理器来定义记录头属性。有关Data Collector 更改的数据处理以及启用CDC的来源的列表的详细信息 ，请参阅 [处理更改的数据。](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

- 操作阶段属性

  您在目标属性中定义默认操作。`sdc.operation.type`未设置记录头属性时，目标使用默认操作 。

  您还可以定义如何使用`sdc.operation.type`header属性中定义的不受支持的操作来处理记录 。目标可以丢弃它们，将它们发送给错误，或使用默认操作。

## 配置GPSS生产者目的地

配置GPSS Producer目标以通过Greenplum流服务器（GPSS）在Greenplum数据库中插入，更新或合并数据。

在管道中使用GPSS Producer目标之前，请完成[所需的先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/GPSS.html#concept_axh_wlz_q3b)。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **GPSS”**选项卡上，配置以下属性：

   | GPSS属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | Greenplum数据库主机                                          | Greenplum Stream Server连接到的Greenplum数据库主机的主机名。 |
   | Greenplum数据库端口                                          | Greenplum Stream Server用于连接Greenplum数据库主服务器的端口。 |
   | GPSS主机                                                     | Greenplum Stream Server的主机名。                            |
   | GPSS端口                                                     | 目的地用于连接Greenplum Stream Server的端口。                |
   | 模式名称                                                     | 包含要写入数据的数据库和表的模式的名称。                     |
   | 数据库名称                                                   | 包含要向其中写入数据的表的数据库的名称。                     |
   | 表名                                                         | 要写入数据的表的名称。                                       |
   | 不支持的操作处理                                             | `sdc.operation.type`不支持在记录头属性中定义的CRUD操作类型时采取的措施 ：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。使用默认操作-使用默认操作将记录写入目标系统。 |
   | [默认操作](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/GPSS.html#concept_alc_2bf_r3b) | 如果`sdc.operation.type`未设置记录头属性，则执行默认的CRUD操作。 |
   | 字段到列的映射                                               | 记录字段和数据库表列之间的映射。默认情况下，目标将字段映射到具有相同名称的列。指定以下属性：列名-数据库表中的列名。SDC字段- 数据收集器记录中的字段。默认值-当记录不包含任何值时写入的值。Greenplum数据类型-要写入的数据类型。如果未指定，则写入在列的架构中指定的数据类型。 |
   | 主键字段                                                     | 指定主键的表列的列表。当映射的记录字段中的值与列出的列中的值匹配时，目标将使用记录中的数据更新或合并数据库行。 |

3. 在“ **凭据”**选项卡上，配置以下属性：

   | 凭证属性        | 描述                                                         |
   | :-------------- | :----------------------------------------------------------- |
   | Greenplum用户名 | 用于访问Greenplum Stream Server和Greenplum数据库的用户名。   |
   | Greenplum密码   | 用户名的密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |