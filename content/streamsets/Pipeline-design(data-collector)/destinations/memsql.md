# MemSQL快速加载程序

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310201928707.png) 资料收集器

MemSQL快速加载程序目标使用JDBC连接通过LOAD语句将数据插入到MemSQL或MySQL数据库表中。

在将数据插入MemSQL或MySQL的管道中，可以使用MemSQL快速加载程序目标而不是JDBC生产者目标来提高性能。但是，在更新或删除数据的管道中，必须使用JDBC Producer目标。MemSQL快速加载程序目标不处理CDC记录；目标将错误处理应用于标头属性中具有CDC操作的记录。

要配置MemSQL快速加载程序目标，请指定连接信息，表名称，并可选地定义驱动程序需要的字段映射和其他属性。默认情况下，MemSQL快速加载程序目标根据匹配的字段名称将数据写入表。您可以通过定义特定的映射来覆盖默认字段映射。要使用低于4.0的JDBC版本，可以指定驱动程序类名称并定义运行状况检查查询。

在使用MemSQL快速加载程序目标之前，必须安装MemSQL阶段库并完成其他[先决任务](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MemSQLLoader.html#memsqlloader_prereqs)。MemSQL 阶段库是一个[Enterprise阶段库](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Installation/EnterpriseStageLibraries.html#concept_s1r_1gg_dhb)，仅供开发用途免费。有关购买用于生产的舞台库的信息，请[联系StreamSets](https://streamsets.com/contact-us/)。

## 先决条件

在使用MemSQL快速加载程序目标之前，请完成以下先决条件：

- [安装MemSQL阶段库](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MemSQLLoader.html#concept_q2c_chg_kgb)。
- 要将MemSQL快速加载程序与MySQL或MemSQL数据库一起使用，[请安装MySQL的JDBC驱动程序](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MemSQLLoader.html#MemSQLLoader-InstallingDriver)。
- 要将MemSQL快速加载程序与MySQL数据库一起使用，必须在MySQL中启用本地数据加载。参见MySQL主题[LOAD DATA LOCAL的安全性问题](https://dev.mysql.com/doc/refman/8.0/en/load-data-local.html)。



### 安装MemSQL阶段库

在使用MemSQL快速加载程序目标之前，您必须安装MemSQL阶段库。

MemSQL 阶段库是一个Enterprise阶段库，仅供开发用途免费。有关购买用于生产的舞台库的信息，请[联系StreamSets](https://streamsets.com/contact-us/)。

您可以使用Package Manager来安装Enterprise阶段库以进行tarball Data Collector的安装，也可以将其作为定制阶段库来进行tarball，RPM或Cloudera Manager Data Collector的 安装。

#### 支持的版本

下表列出了与特定的Data Collector 版本一起使用的MemSQL Enterprise阶段库的版本：

| 数据收集器版本                 | 支持的舞台库版本  |
| :----------------------------- | :---------------- |
| Data Collector 3.8.x及更高版本 | MemSQL企业库1.0.1 |
| 数据收集器 3.7.x               | MemSQL企业库1.0.0 |

#### 使用软件包管理器安装

您可以使用Package Manager在tarball Data Collector 安装中安装MemSQL阶段库。

1. 单击“程序包管理器”图标：![img](imgs/icon_PackageManager-20200310201928297.png)。

2. 在导航面板中，单击**Enterprise Stage Libraries**。

3. 选择**MemSQL企业库**，然后单击 **安装**图标：![img](imgs/icon_InstallLib-20200310201928461.png)。

4. 阅读StreamSets 订阅服务条款。如果您同意，请选中复选框，然后单击“ **安装”**。

   Data Collector将安装所选的舞台库。

5. 重新启动Data Collector。

#### 作为自定义舞台库安装

您可以在tarball，RPM或Cloudera Manager Data Collector 安装中将MemSQL Enterprise阶段库安装为自定义阶段库。

1. 要下载舞台库，请转到[StreamSets下载企业连接器](https://streamsets.com/download/enterprise-connectors/)页面。

   该网页显示按发布日期组织的Enterprise阶段库，并在页面顶部显示最新版本。

2. 单击您要下载的Enterprise阶段库名称和版本。

3. 在“ **下载企业连接器”**表单中，输入您的姓名和联系信息。

4. 阅读StreamSets订阅服务条款。如果您同意，请接受服务条款，然后单击“ **提交”**。

   舞台库下载。

5. 将Enterprise阶段库安装和管理为自定义阶段库。

   有关更多信息，请参见[Custom Stage Libraries](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/CustomStageLibraries.html#concept_pmc_jk1_1x)。

### 安装用于MemSQL快速加载程序的JDBC驱动程序

MemSQL快速加载程序目标要求您安装MySQL的JDBC驱动程序。无论使用的是MemSQL还是MySQL，都应为MySQL安装JDBC驱动程序。在安装此驱动程序之前，目标无法访问MemSQL或MySQL数据库。

**注意：** StreamSets已使用带有MySQL Connector / J 8.0.12驱动程序的MemSQL 6.5.16测试了目标。

1. 下载用于MySQL的JDBC驱动程序。

   您可以在MySQL网站的“ [下载连接器/ J”](https://dev.mysql.com/downloads/connector/j/)页面上下载驱动程序。

2. 将驱动程序安装为MemSQL Enterprise阶段库的外部库。

   有关安装其他驱动程序的信息，请参阅 Data Collector 文档 中的“ [安装外部库](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/ExternalLibs.html%23concept_pdv_qlw_ft) ”。

## 配置MemSQL快速加载程序目标

配置MemSQL快速加载程序目标，以使用JDBC将数据插入到MemSQL或MySQL数据库表中。不要使用此目标来更新或删除数据，或处理CDC记录。

在管道中使用MemSQL Fast Loader目标之前，请完成 [所需的先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MemSQLLoader.html#memsqlloader_prereqs)。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **JDBC”**选项卡上，配置以下属性：

   | JDBC属性         | 描述                                                         |
   | :--------------- | :----------------------------------------------------------- |
   | JDBC连接字符串   | 用于连接数据库的连接字符串。                                 |
   | 使用凭证         | 在“凭据”选项卡上启用输入凭据。在JDBC连接字符串中不包括凭据时使用。 |
   | 模式名称         | 要使用的可选数据库或架构名称。当数据库需要标准表名时使用。   |
   | 表名             | 要使用的数据库表名称。使用数据库所需的表名格式。             |
   | 字段到列的映射   | 用于覆盖默认字段到列的映射。默认情况下，字段被写入具有相同名称的列。覆盖映射时，可以定义参数化的值，以在将SQL函数写入字段值之前将SQL函数应用于字段值。例如，要将字段值转换为整数，请为参数化值输入以下内容：`CAST(? AS INTEGER)`问号（？）替换为字段的值。保留默认值？如果您不需要应用SQL函数。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击**添加**图标以创建其他字段到列的映射。 |
   | 重复键错误处理   | 记录重复表中行的主键时要采取的措施：忽略–丢弃记录并保留现有行。替换–用记录覆盖现有行。 |
   | 其他JDBC配置属性 | 要使用的其他JDBC配置属性。要添加属性，请单击 **添加**并定义JDBC属性名称和值。使用JDBC期望的属性名称和值。 |

3. 如果在**JDBC**选项卡上将源配置为与JDBC连接字符串分开输入JDBC凭据，则在“ **凭据”** 选项卡上配置以下属性：

   | 凭证属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 用户名   | JDBC连接的用户名。                                           |
   | 密码     | JDBC帐户的密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |

4. 使用低于4.0的JDBC版本时，在“ **旧版驱动程序”**选项卡上，可以选择配置以下属性：

   | 旧版驱动程序属性     | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | JDBC类驱动程序名称   | JDBC驱动程序的类名。早于版本4.0的JDBC版本必需。              |
   | 连接运行状况测试查询 | 可选查询，用于测试连接的运行状况。仅当JDBC版本低于4.0时才建议使用。 |

5. 在“ **高级”**选项卡上，可以选择配置高级属性。

   这些属性的默认值在大多数情况下都应该起作用：

   | 先进物业     | 描述                                                         |
   | :----------- | :----------------------------------------------------------- |
   | 最大游泳池   | 创建的最大连接数。默认值为1。建议值为1。                     |
   | 最小空闲连接 | 创建和维护的最小连接数。要定义固定连接池，请设置为与“最大池大小”相同的值。默认值为1。 |
   | 连接超时     | 等待连接的最长时间。在表达式中使用时间常数来定义时间增量。默认值为30秒，定义如下：`${30 * SECONDS}` |
   | 空闲超时     | 允许连接空闲的最长时间。在表达式中使用时间常数来定义时间增量。使用0以避免删除任何空闲连接。当输入的值接近或超过连接的最大生存期时，Data Collector将忽略空闲超时。默认值为10分钟，定义如下：`${10 * MINUTES}` |
   | 最大连接寿命 | 连接的最大寿命。在表达式中使用时间常数来定义时间增量。使用0设置最大寿命。设置最大寿命时，最小有效值为30分钟。默认值为30分钟，定义如下：`${30 * MINUTES}` |
   | 交易隔离     | 用于连接数据库的事务隔离级别。默认是为数据库设置的默认事务隔离级别。您可以通过将级别设置为以下任意值来覆盖数据库默认值：阅读已提交阅读未提交可重复读可序列化 |
   | 初始化查询   | 在阶段连接到数据库后立即执行的SQL查询。用于根据需要设置数据库会话。例如，以下查询为MySQL数据库设置会话的时区： `SET time_zone = timezone;` |