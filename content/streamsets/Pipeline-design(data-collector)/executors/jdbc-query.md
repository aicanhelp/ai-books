# JDBC查询执行器

JDBC Query执行程序通过JDBC连接到数据库，并在每次收到事件记录时执行用户定义的SQL查询。将JDBC Query执行程序用作管道中事件流的一部分。

JDBC查询执行程序可以在每次批处理之后提交到数据库，或设置自动提交模式。在自动提交模式下，数据库为每个记录提交数据。默认情况下，执行程序在每个批处理之后提交。

配置JDBC Query执行程序时，可以指定JDBC连接属性和要使用的查询。您可以配置驱动程序需要的自定义属性，高级连接属性以及要使用的提交类型。您还可以使执行程序并行运行插入和删除查询，以提高吞吐量。

要使用低于4.0的JDBC版本，可以指定驱动程序类名称并定义运行状况检查查询。

您还可以配置执行程序以为另一个事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

## 数据库供应商和驱动程序

JDBC Query执行程序可以从多个数据库供应商读取数据库数据。

StreamSets已使用以下数据库供应商，版本和JDBC驱动程序测试了该阶段：

| 数据库供应商        | 版本和驱动程序                                               |
| :------------------ | :----------------------------------------------------------- |
| 的MySQL             | 带有MySQL Connector / J 8.0.12驱动程序的MySQL 5.7带有MySQL Connector / J 8.0.12驱动程序的MySQL 8.0 |
| PostgreSQL的        | PostgreSQL 9.4.18PostgreSQL 9.6.2PostgreSQL 9.6.9PostgreSQL 10.4连接到PostgreSQL数据库时，不需要安装JDBC驱动程序。Data Collector包括PostgreSQL所需的JDBC驱动程序。 |
| 甲骨文              | 带有Oracle 11.2.0 JDBC驱动程序的Oracle 11g                   |
| Microsoft SQL服务器 | SQL Server 2017连接到Microsoft SQL Server时，不需要安装JDBC驱动程序。Data Collector包括SQL Server所需的JDBC驱动程序。 |

## 安装JDBC驱动程序

在使用JDBC Query执行程序之前，请为数据库安装JDBC驱动程序。您必须安装所需的驱动程序才能访问数据库。

有关安装其他驱动程序的信息，请参阅 Data Collector 文档 中的“ [安装外部库](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/ExternalLibs.html%23concept_pdv_qlw_ft) ”。

## 事件产生



JDBC Query执行程序可以生成可在事件流中使用的事件。启用事件生成后，执行程序将为每个成功或失败的查询生成事件。

JDBC查询事件可以任何逻辑方式使用。例如：

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录



JDBC Query执行程序生成的事件记录具有以下与事件相关的记录头属性。记录标题属性存储为字符串值。

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下事件类型：成功查询-查询成功完成后生成。failed-query-查询失败后生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

JDBC Query执行程序可以生成以下类型的事件记录：

- 查询成功

  执行程序在成功完成查询后会生成一个成功查询事件记录。成功查询事件记录的`sdc.event.type` 记录头属性设置为`sucessful-query`，包括以下字段：活动栏位名称描述询问查询完成。查询结果受查询影响的行数。如果选择了“在事件中包括查询结果计数”属性，则包括此属性。

- 查询失败

  执行程序无法完成查询后，将生成查询失败事件记录。查询失败事件记录的`sdc.event.type` 记录头属性设置为，`failed-query`并包含以下字段：活动栏位名称描述询问尝试查询。

## 配置JDBC查询执行器

将JDBC Query执行程序配置为事件流的一部分，以在每次接收到事件记录时执行数据库查询。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/JDBCQuery.html#concept_j2c_hpx_gjb) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击**添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **JDBC”**选项卡上，配置以下属性：

   | JDBC属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | JDBC连接字符串                                               | 用于连接数据库的连接字符串。某些数据库（例如PostgreSQL）需要连接字符串中的模式。使用数据库所需的连接字符串格式。 |
   | 使用凭证                                                     | 在“凭据”选项卡上启用输入凭据。在JDBC连接字符串中不包括凭据时使用。 |
   | SQL查询                                                      | 执行程序每次接收到事件记录时都要执行的查询。                 |
   | [在事件中包括查询结果计数](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/JDBCQuery.html#concept_asb_zyx_gjb) | 在生成的事件记录中包括受查询影响的行数。                     |
   | 其他JDBC配置属性                                             | 要使用的其他JDBC配置属性。要添加属性，请单击 **添加**并定义JDBC属性名称和值。使用JDBC期望的属性名称和值。 |

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
   | 启用并行查询 | 并行运行插入和删除查询以提高吞吐量。选中后，执行程序将在与数据库的所有已配置连接上同时运行插入和删除查询，但会继续串行运行其他查询。执行程序先运行插入查询，然后运行其他查询，然后删除查询。执行程序在每个语句之后提交数据。为了获得最佳性能，请在选择“启用并行查询”时选择“自动提交”。 |
   | 连接超时     | 等待连接的最长时间。在表达式中使用时间常数来定义时间增量。默认值为30秒，定义如下：`${30 * SECONDS}` |
   | 空闲超时     | 允许连接空闲的最长时间。在表达式中使用时间常数来定义时间增量。使用0以避免删除任何空闲连接。当输入的值接近或超过连接的最大生存期时，Data Collector将忽略空闲超时。默认值为10分钟，定义如下：`${10 * MINUTES}` |
   | 最大连接寿命 | 连接的最大寿命。在表达式中使用时间常数来定义时间增量。使用0设置最大寿命。设置最大寿命时，最小有效值为30分钟。默认值为30分钟，定义如下：`${30 * MINUTES}` |
   | 批量提交     | 确定执行程序是否在每个批处理之后提交到数据库。默认启用。     |
   | 自动提交     | 确定是否启用自动提交模式。在自动提交模式下，数据库为每个记录提交数据。默认设置为禁用。 |
   | 交易隔离     | 用于连接数据库的事务隔离级别。默认是为数据库设置的默认事务隔离级别。您可以通过将级别设置为以下任意值来覆盖数据库默认值：阅读已提交阅读未提交可重复读可序列化 |
   | 初始化查询   | 在阶段连接到数据库后立即执行的SQL查询。用于根据需要设置数据库会话。例如，以下查询为MySQL数据库设置会话的时区： `SET time_zone = timezone;` |