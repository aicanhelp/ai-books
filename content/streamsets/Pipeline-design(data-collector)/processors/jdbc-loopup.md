# JDBC查询

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310181037589.png) 资料收集器

JDBC查找处理器使用JDBC连接在数据库表中执行查找，并将查找值传递给字段。使用JDBC查找可使用其他数据丰富记录。

例如，您可以将处理器配置为使用department_ID字段作为列，以在数据库表中查找部门名称值，并将这些值传递给新的department_name输出字段。

当查找导致多个匹配时，JDBC查找处理器可以返回第一个匹配值，单个记录中列表中的所有匹配值或单独记录中的所有匹配值。

在配置JDBC查找时，可以指定连接信息和自定义JDBC配置属性来确定处理器如何连接到数据库。您可以配置SQL查询来定义要在数据库中查找的数据，指定要向其中写入查找值的输出字段，然后选择多重匹配行为。您可以配置当查询不返回任何值时的行为，还可以为相同情况配置默认值。

您可以将处理器配置为本地缓存查找值，以提高性能。在缓存查找值时，您还可以启用使用其他线程来预填充查找缓存并进一步提高性能。

要使用低于4.0的JDBC版本，可以指定驱动程序类名称并定义运行状况检查查询。

## 数据库供应商和驱动程序

JDBC查找处理器可以执行来自多个数据库供应商的数据库数据的查找。

StreamSets已使用以下数据库供应商，版本和JDBC驱动程序测试了该阶段：

| 数据库供应商        | 版本和驱动程序                                               |
| :------------------ | :----------------------------------------------------------- |
| 的MySQL             | 带有MySQL Connector / J 8.0.12驱动程序的MySQL 5.7带有MySQL Connector / J 8.0.12驱动程序的MySQL 8.0 |
| PostgreSQL的        | PostgreSQL 9.4.18PostgreSQL 9.6.2PostgreSQL 9.6.9PostgreSQL 10.4连接到PostgreSQL数据库时，不需要安装JDBC驱动程序。Data Collector包括PostgreSQL所需的JDBC驱动程序。 |
| 甲骨文              | 带有Oracle 11.2.0 JDBC驱动程序的Oracle 11g                   |
| Microsoft SQL服务器 | SQL Server 2017连接到Microsoft SQL Server时，不需要安装JDBC驱动程序。Data Collector包括SQL Server所需的JDBC驱动程序。 |

## 安装JDBC驱动程序

在使用JDBC查找处理器之前，请为数据库安装JDBC驱动程序。您必须安装所需的驱动程序才能访问数据库。

**注意：** 连接到PostgreSQL数据库时，不需要安装JDBC驱动程序。Data Collector包括PostgreSQL所需的JDBC驱动程序。

有关安装其他驱动程序的信息，请参阅 Data Collector 文档 中的“ [安装外部库](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/ExternalLibs.html%23concept_pdv_qlw_ft) ”。

## 查找缓存

为了提高管道性能，可以将JDBC查找处理器配置为本地缓存从数据库表返回的值。

处理器缓存值，直到缓存达到最大大小或到期时间。当达到第一个限制时，处理器会将缓存中的值逐出。

您可以配置以下方式从缓存中逐出值：

- 基于规模的驱逐

  配置处理器缓存的最大值数量。当达到最大数量时，处理器将从高速缓存中逐出最旧的值。

- 基于时间的驱逐

  配置一个值可以保留在缓存中而不被写入或访问的时间。当达到到期时间时，处理器将从高速缓存中逐出该值。驱逐策略确定处理器是否测量自上次写入值或自上次访问值以来的到期时间。

  例如，您将逐出策略设置为在上次访问后到期，并将到期时间设置为60秒。处理器在60秒钟内未访问任何值后，处理器将从高速缓存中逐出该值。

当您停止管道时，处理器将清除缓存。

### 使用其他线程

使用本地缓存时，可以增加JDBC查找处理器用来预填充查找缓存的线程数。填充缓存后，将释放其他线程。这可以大大提高处理器的性能。

默认情况下，“高级”选项卡上的“最小空闲连接数”属性确定Data Collector 创建和维护的与数据库的最小连接数。

当启用使用本地查找缓存时，“最小空闲连接数”属性还可以确定 处理器用于线程预填充缓存的Data Collector计算机上的可用核心数。

启用缓存后，JDBC查找处理器将根据以下较小的数字使用其他线程：

- 配置的“最小空闲连接数”属性。
- Data Collector计算机上的可用核心数减一。处理器永远不会使用所有可用的内核。

通过增加“最小空闲连接数”属性的设置，可以使处理器能够将Data Collector 计算机上几乎所有可用的内核用于其他线程，以预填充查找缓存。

例如，假设您 在启动管道时在Data Collector机器上有8个可用核心，并且JDBC查找启用了本地缓存并且“最小空闲连接”属性设置为8。然后，JDBC查找可以使用7个可用核心使线程预填充查找缓存。如果您要尽快完成复杂的查找处理并且不需要为其他处理保留资源，那么这可能是理想的选择。

要为其他处理保留机器资源，可以通过将“最小空闲连接数”属性设置为较小的数量来限制处理器使用的内核。例如，如果将“最小空闲连接数”设置为5，则处理器最多可以将4个可用内核用于线程。

### 重试查找缺少的值

启用本地缓存时，当对给定列的查找失败并且在“列映射”中为该列定义了默认值时，处理器还将缓存配置的默认值。然后，处理器始终返回该列的默认值，以避免不必要的查找。

您可以通过启用“对缺失值重试”属性，将处理器配置为对已知缺失值重试查找。将处理器配置为在管道运行时可能更新查找表时重试查找。

例如，如果您希望在管道运行时将新值插入表中，则需要配置处理器以重试请求，而不是返回缓存的默认值。

**注意：**如果对给定列的查找失败，并且未为该列配置默认值，则处理器将根据“缺失值行为”属性处理记录。

## 配置JDBC查找

配置JDBC查找处理器以在数据库表中执行查找。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **JDBC”**选项卡上，配置以下属性：

   | JDBC属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | JDBC连接字符串                                               | 用于连接数据库的连接字符串。某些数据库（例如PostgreSQL）需要连接字符串中的模式。使用数据库所需的连接字符串格式。 |
   | 使用凭证                                                     | 在“凭据”选项卡上启用输入凭据。在JDBC连接字符串中不包括凭据时使用。 |
   | SQL查询                                                      | 用于在数据库中查找数据的SQL查询。使用以下语法进行查询：`SELECT ,  FROM  WHERE  =    '${record:value()}'`例如，要使用部门ID字段查找部门名称列，请使用以下查询：`SELECT DeptName FROM Departments WHERE DeptID = '${record:value('/dept_ID')}'` |
   | 列映射                                                       | 用于覆盖默认列到字段的映射。默认情况下，列被写入具有相同名称的字段。输入以下内容：列名称-包含查找值的数据库列的名称。输入列名或输入定义该列名的表达式。SDC字段-记录中接收查找值的字段的名称。您可以指定现有字段或新字段。如果该字段不存在，则JDBC查找将创建该字段。默认值-查询不返回字段值时使用的可选默认值。如果查询不返回任何值，并且未定义此属性，则处理器将根据“缺失值行为”属性处理记录。要为日期数据类型输入默认值，请使用以下格式：yyyy / MM / dd。要为Datetime数据类型输入默认值，请使用以下格式：yyyy / MM / dd HH：mm：ss。数据类型-用于SDC字段的数据类型。指定默认值时为必需。处理器默认使用数据库列数据类型。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以创建其他列映射。 |
   | 多值行为                                                     | 找到多个匹配值时要采取的措施：仅第一个值-返回第一个值。所有值作为列表-在单个记录中返回列表中的每个匹配值。拆分为多个记录-返回单独记录中的每个匹配值。 |
   | 价值观缺失行为                                               | 在未定义默认值的字段中找不到返回值时采取的措施：发送到错误-将记录发送到错误。沿管道传递记录不变-传递没有查找返回值的记录。 |
   | 最大布料大小（字符）                                         | Clob字段中要读取的最大字符数。较大的数据将被截断。           |
   | 最大Blob大小（字节）                                         | Blob字段中要读取的最大字节数。                               |
   | 启用本地缓存 [![img](imgs/icon_moreInfo-20200310181037670.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/JDBCLookup.html#concept_jt5_kx2_px) | 指定是否在本地缓存返回的值。                                 |
   | 缓存的最大条目数                                             | 要缓存的最大值数。当达到最大数量时，处理器将从高速缓存中逐出最旧的值。默认值为-1，表示无限制。 |
   | 驱逐政策类型                                                 | 过期时间过后，用于从本地缓存中逐出值的策略：上次访问后过期-计算自上次通过读取或写入访问值以来的过期时间。上次写入后过期-测量自创建值或上次替换值以来的过期时间。 |
   | 到期时间                                                     | 一个值可以保留在本地缓存中而没有被访问或写入的时间。默认值为1秒。 |
   | 时间单位                                                     | 到期时间的时间单位。默认值为秒。                             |
   | 重试缺失值 [![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_moreInfo.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/JDBCLookup.html#concept_xdx_xqq_vcb) | 指定是否重试查找已知缺失值。默认情况下，处理器缓存，然后始终返回已知缺失值的默认值，以避免不必要的查找。 |
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

   | 先进物业         | 描述                                                         |
   | :--------------- | :----------------------------------------------------------- |
   | 最大游泳池       | 创建的最大连接数。默认值为1。建议值为1。                     |
   | 最小空闲连接     | 如果未启用本地缓存，则确定要创建和维护的与数据库的最小连接数。启用本地缓存后，还要确定可用于其他线程进行处理的可用核心数。**注意：**启用本地缓存后，请仔细配置此属性，以避免独占Data Collector资源。有关更多信息，请参见《[使用附加线程》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/JDBCLookup.html#concept_vsl_dvt_d2b)。 |
   | 连接超时         | 等待连接的最长时间。在表达式中使用时间常数来定义时间增量。默认值为30秒，定义如下：`${30 * SECONDS}` |
   | 空闲超时         | 允许连接空闲的最长时间。在表达式中使用时间常数来定义时间增量。使用0以避免删除任何空闲连接。当输入的值接近或超过连接的最大生存期时，Data Collector将忽略空闲超时。默认值为10分钟，定义如下：`${10 * MINUTES}` |
   | 最大连接寿命     | 连接的最大寿命。在表达式中使用时间常数来定义时间增量。使用0设置最大寿命。设置最大寿命时，最小有效值为30分钟。默认值为30分钟，定义如下：`${30 * MINUTES}` |
   | 自动提交         | 确定是否启用自动提交模式。在自动提交模式下，数据库为每个记录提交数据。默认设置为禁用。 |
   | 强制执行只读连接 | 创建只读连接以避免任何类型的写入。默认启用。不建议禁用此属性。 |
   | 交易隔离         | 用于连接数据库的事务隔离级别。默认是为数据库设置的默认事务隔离级别。您可以通过将级别设置为以下任意值来覆盖数据库默认值：阅读已提交阅读未提交可重复读可序列化 |
   | 初始化查询       | 在阶段连接到数据库后立即执行的SQL查询。用于根据需要设置数据库会话。例如，以下查询为MySQL数据库设置会话的时区： `SET time_zone = timezone;` |