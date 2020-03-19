# JDBC三通

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310181102864.png) 资料收集器

JDBC Tee处理器使用JDBC连接将数据写入数据库表，然后将生成的数据库列值传递给字段。使用JDBC Tee将部分或全部记录字段写入数据库表，然后用其他数据丰富记录。

要使用JDBC Tee，必须将数据库表配置为在插入数据时生成列值。

**注意：** Oracle JDBC驱动程序不会以标准方式返回自动生成的键。因此，JDBC Tee处理器不支持Oracle。

在配置JDBC Tee时，您可以指定连接信息，表名，并可以选择定义字段映射。默认情况下，JDBC Tee根据匹配的字段名称将数据写入表。您可以通过定义特定的映射来覆盖默认字段映射。

您定义生成的列映射以指定将生成的数据库列值传递到的输出字段。

您可以将阶段配置为在写入部分批处理时发生错误时回滚整个批处理。您还可以配置驱动程序需要的自定义属性。

JDBC Tee处理器可以使用在`sdc.operation.type`记录头属性中定义的CRUD操作 来写入数据。您可以为没有标题属性或值的记录定义默认操作。您还可以配置是否对插入和删除使用多行操作，以及如何使用不支持的操作处理记录。

处理来自启用CDC的原始数据时，可以指定原始更改日志以辅助记录处理。有关Data Collector更改数据处理以及启用CDC的来源的列表的信息，请参见[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

要使用低于4.0的JDBC版本，可以指定驱动程序类名称并定义运行状况检查查询。

## 例

假设您正在处理客户订单。您有一个带有ID列作为主键的客户数据库表。客户表配置为在将每一行插入表中时为ID列生成序列号。例如，第一个客户行的ID为001，第二个客户行的ID为002。

当您处理新客户的订单时，JDBC Tee将客户数据插入到客户表中，并且数据库返回该客户的生成ID。JDBC Tee将生成的ID值传递给记录中的新cust_ID字段。处理器将所有记录字段传递到管道中的下一级，以进行其他处理。

下图显示了JDBC Tee如何处理我们的客户订单示例的高级概述：

![img](imgs/JDBCTee.png)

## 数据库供应商和驱动程序

JDBC Tee处理器可以将数据写入多个数据库供应商。

StreamSets已使用以下数据库供应商，版本和JDBC驱动程序测试了该阶段：

| 数据库供应商        | 版本和驱动程序                                               |
| :------------------ | :----------------------------------------------------------- |
| 的MySQL             | 带有MySQL Connector / J 8.0.12驱动程序的MySQL 5.7带有MySQL Connector / J 8.0.12驱动程序的MySQL 8.0 |
| PostgreSQL的        | PostgreSQL 9.4.18PostgreSQL 9.6.2PostgreSQL 9.6.9PostgreSQL 10.4连接到PostgreSQL数据库时，不需要安装JDBC驱动程序。Data Collector包括PostgreSQL所需的JDBC驱动程序。 |
| 甲骨文              | 带有Oracle 11.2.0 JDBC驱动程序的Oracle 11g                   |
| Microsoft SQL服务器 | SQL Server 2017连接到Microsoft SQL Server时，不需要安装JDBC驱动程序。Data Collector包括SQL Server所需的JDBC驱动程序。 |

## 安装JDBC驱动程序

在使用JDBC Tee处理器之前，请为数据库安装JDBC驱动程序。您必须安装所需的驱动程序才能访问数据库。

**注意：** 连接到PostgreSQL数据库时，不需要安装JDBC驱动程序。Data Collector包括PostgreSQL所需的JDBC驱动程序。

有关安装其他驱动程序的信息，请参阅 Data Collector 文档 中的“ [安装外部库](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/ExternalLibs.html%23concept_pdv_qlw_ft) ”。

## 定义CRUD操作

JDBC Tee处理器可以插入，更新或删除数据。处理器根据在CRUD操作标头属性或与操作相关的阶段属性中定义的CRUD操作写入记录。

您可以通过以下方式定义CRUD操作：

- CRUD操作标头属性

  您可以在CRUD操作记录标题属性中定义CRUD操作。处理器在sdc.operation.type记录头属性中查找要使用的CRUD操作。

  该属性可以包含以下数值之一：INSERT为12个代表删除3更新

  如果您的管道包括启用CRUD的原始数据源，该原始数据元处理已更改的数据，则处理器仅从原始数据源的sdc.operation.type标头属性中读取操作类型。如果管道使用非CDC来源，则可以使用表达式评估器或脚本处理器来定义记录头属性。有关Data Collector更改的数据处理以及启用CDC的来源的列表的更多信息，请参见[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

- 操作阶段属性

  您在处理器属性中定义默认操作。未设置sdc.operation.type记录头属性时，处理器将使用默认操作。

  您还可以定义如何使用sdc.operation.type标头属性中定义的不受支持的操作来处理记录。处理器可以丢弃它们，将它们发送给错误或使用默认操作。

## 单行和多行操作

JDBC Tee默认执行单行操作。也就是说，它为每个记录执行一条SQL语句。当目标数据库支持时，可以将JDBC Tee配置为执行多行操作。根据数据的顺序，多行操作可以提高管道性能。

执行多行操作时，JDBC Tee为顺序插入行和顺序删除行创建单个SQL语句。JDBC Tee不执行多行更新操作。您可以配置“语句参数限制”属性来限制插入操作中的参数数量-也就是说，您可以限制插入语句中包含的记录数量。

例如，假设管道生成三个插入记录，然后生成两个更新记录和四个删除记录。如果启用了多行操作并且未设置语句参数限制，则JDBC Tee会为三个插入记录生成一个插入SQL语句，为两个更新语句生成一个-每个更新记录一个，并为单个删除语句生成一个delete语句。四个删除记录。另一方面，如果启用多行操作并将语句参数限制设置为2，则JDBC Tee会生成两个插入SQL语句-一个用于两个插入记录，一个用于第三条插入记录，两个更新语句-每个用于一个更新记录，以及四个删除记录的单个删除语句。

**重要：**在启用多行操作之前，请验证数据库是否支持JDBC Tee使用的SQL语句。

多行操作的错误处理取决于数据库。如果数据库在多行语句中报告导致错误的单个记录，则该阶段将该记录发送到错误流。如果数据库未报告哪个记录导致错误，则该阶段将语句中的所有记录发送到错误流。

对于多行插入，JDBC Tee使用以下SQL语句：

```
INSERT INTO <table name> (<col1>, <col2>, <col3>) 
     VALUES (<record1 field1>,<record1 field2>,<record1 field3>), 
     (<r2 f1>,<r2 f2>,<r2 f3>), (<r3 f1>,<r3 f2>,<r3 f3>),...;
```

对于多行删除，JDBC Tee对具有单个主键的表使用以下SQL语句：

```
DELETE FROM <table name> WHERE <primary key> IN (<key1>, <key2>, <key3>,...);
```

对于多行删除，JDBC Tee对具有多个主键的表使用以下SQL语句：

```
DELETE FROM <table name> WHERE (<pkey1>, <pkey2>, <pkey3>)
      IN ((<key1-1>, <key1-2>, <key1-3>),(<key2-1>, <key2-2>, <key2-2>),...);
```

## 配置JDBC Tee处理器

配置JDBC Tee处理器，以将数据写入数据库表，并用生成的数据库列中的数据丰富记录。

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
   | JDBC连接字符串                                               | 用于连接数据库的连接字符串。使用数据库所需的连接字符串格式。 |
   | 使用凭证                                                     | 在“凭据”选项卡上启用输入凭据。在JDBC连接字符串中不包括凭据时使用。 |
   | 表名                                                         | 要写入的数据库表。某些数据库需要完全限定的表名，例如`:`。使用数据库所需的表名格式。输入以下内容之一：现有数据库表的名称。该表达式的计算结果为现有数据库表的名称。例如，如果表名称存储在“ tableName”记录属性中，请输入以下表达式：`${record:attribute('tableName')}`如果该表不存在，则处理器将创建该表。 |
   | 字段到列的映射                                               | 用于覆盖默认字段到列的映射。默认情况下，字段被写入具有相同名称的列。覆盖映射时，可以定义参数化的值，以在将SQL函数写入字段值之前将SQL函数应用于字段值。例如，要将字段值转换为整数，请为参数化值输入以下内容：`CAST(? AS INTEGER)`问号（？）替换为字段的值。保留默认值？如果您不需要应用SQL函数。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击**添加**图标以创建其他字段到列的映射。 |
   | 生成的列映射                                                 | 将生成的数据库列映射到记录中的字段。输入以下内容：列名。包含生成值的数据库列的名称。输入列名或输入定义该列的表达式。SDC字段。记录中接收生成的列值的字段的名称。您可以指定现有字段或新字段。如果该字段不存在，则JDBC Tee将创建该字段。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以创建其他生成的列映射。 |
   | 更改日志格式                                                 | 变更捕获数据的格式。在处理变更捕获数据时使用。               |
   | [默认操作](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/JDBCTee.html#concept_qfd_tpm_5y) | 如果`sdc.operation.type`未设置记录头属性，则执行默认的CRUD操作。 |
   | 不支持的操作处理                                             | `sdc.operation.type`不支持在记录头属性中定义的CRUD操作类型时采取的措施 ：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。使用默认操作-使用默认操作将记录写入目标系统。 |
   | [使用多行操作](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/JDBCTee.html#concept_jnl_rmp_h1b) | 将顺序插入操作和顺序删除操作组合为单个语句。选择以同时插入和删除多个记录。在启用此选项之前，请验证数据库是否支持该阶段使用的多行SQL语句。默认情况下，该阶段执行单行操作。 |
   | 语句参数限制                                                 | 预准备语句中允许用于多行插入的参数数。使用-1禁用参数限制。默认值为-1。 |
   | 回滚批处理出错                                               | 批处理中发生错误时回滚整个批处理。                           |
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
   | 连接超时     | 等待连接的最长时间。在表达式中使用时间常数来定义时间增量。默认值为30秒，定义如下：`${30 * SECONDS}` |
   | 空闲超时     | 允许连接空闲的最长时间。在表达式中使用时间常数来定义时间增量。使用0以避免删除任何空闲连接。当输入的值接近或超过连接的最大生存期时，Data Collector将忽略空闲超时。默认值为10分钟，定义如下：`${10 * MINUTES}` |
   | 最大连接寿命 | 连接的最大寿命。在表达式中使用时间常数来定义时间增量。使用0设置最大寿命。设置最大寿命时，最小有效值为30分钟。默认值为30分钟，定义如下：`${30 * MINUTES}` |
   | 交易隔离     | 用于连接数据库的事务隔离级别。默认是为数据库设置的默认事务隔离级别。您可以通过将级别设置为以下任意值来覆盖数据库默认值：阅读已提交阅读未提交可重复读可序列化 |
   | 初始化查询   | 在阶段连接到数据库后立即执行的SQL查询。用于根据需要设置数据库会话。例如，以下查询为MySQL数据库设置会话的时区： `SET time_zone = timezone;` |