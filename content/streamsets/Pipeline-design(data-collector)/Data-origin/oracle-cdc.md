# Oracle CDC客户端

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310173403783.png) 资料收集器

Oracle CDC客户端进程处理由Oracle LogMiner重做日志提供的更改数据捕获（CDC）信息。使用Oracle CDC客户端处理来自Oracle 11g，12c或18c的数据。

您可以使用此来源执行数据库复制。您可以将单独的管道与JDBC查询使用者或JDBC多表使用者起源一起使用，以读取现有数据。然后以 Oracle CDC客户端源启动管道，以处理后续更改。

Oracle CDC客户端根据提交编号以升序处理数据。

要读取重做日志，Oracle CDC Client需要LogMiner词典。来源可以在重做日志或在线目录中使用字典。在重做日志中使用字典时，源会捕获并调整以适应模式更改。使用重做日志字典时，原点也可以生成事件。

源可以为数据库中的一个或多个表的INSERT，UPDATE，SELECT_FOR_UPDATE和DELETE操作创建记录。您可以选择要使用的操作。原始记录的记录头属性中还包含CDC和CRUD信息，因此生成的记录可以由启用CRUD的目标轻松处理。有关Data Collector 更改的数据处理的概述以及启用CRUD的目标的列表，请参阅[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

**注意：**要使用Oracle CDC Client，必须为要使用的数据库启用LogMiner，并完成必要的先决任务。源使用JDBC访问数据库。

源可以为事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

配置Oracle CDC Client时，您将配置更改数据捕获详细信息，例如要读取的架构和表，如何读取初始更改，字典源位置以及要包括的操作。您还可以指定要使用的事务窗口和LogMiner会话窗口。

您可以将源配置为在本地缓冲记录或使用数据库缓冲区。在使用本地缓冲区之前，请验证所需的资源是否可用，并指定要对未提交的事务执行的操作。

您可以指定当起点遇到不受支持的数据类型时的行为，并且可以配置起点在从补充日志记录数据接收空值时传递空值。当源数据库具有高精度时间戳时，可以将源配置为写入字符串值，而不是日期时间值，以保持精度。

您还可以指定JDBC连接信息和用户凭据。如果架构是在可插拔数据库中创建的，请说明可插拔数据库的名称。您可以配置驱动程序所需的自定义属性。

您可以配置高级连接属性。要使用低于4.0的JDBC版本，请指定驱动程序类名称并定义运行状况检查查询。

当考虑性能时，例如处理非常宽的表时，可以考虑使用默认原始行为的几种替代方法。您可以使用备用PEG解析器，也可以使用多个线程进行解析。或者，您可以将源配置为不解析SQL查询语句，以便将查询传递 给要解析的[SQL Parser处理器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/SQLParser.html#concept_zh2_kfj_tdb)。

## LogMiner词典源

LogMiner提供字典来帮助处理重做日志。LogMiner可以将字典存储在多个位置。

Oracle CDC客户端可以使用以下词典源位置：

- 联机目录-预期表结构不会更改时，请使用联机目录。

- 重做日志-当期望表结构发生更改时，请使用重做日志。从重做日志中读取字典时，Oracle CDC客户端起源确定何时发生架构更改，并刷新用于创建记录的架构。源也可以为其在重做日志中读取的每个DDL生成事件。

  **重要：**在重做日志中使用字典时，请确保每次表结构发生更改时，将最新的字典提取到重做日志中。有关更多信息，请参阅[任务4。提取Log Miner词典（重做日志）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_ypm_fv1_vy)。

请注意，与在联机目录中使用字典相比，在重做日志中使用字典可能会有更高的延迟。但是使用在线目录不允许更改架构。

有关字典选项和配置LogMiner的更多信息，请参见Oracle LogMiner文档。

## Oracle CDC客户端先决条件

在使用Oracle CDC Client源之前，请完成以下任务：

1. 启用LogMiner。
2. 为数据库或表启用补充日志记录。
3. 创建具有所需角色和特权的用户帐户。
4. 要在重做日志中使用字典，请提取Log Miner字典。
5. 安装Oracle JDBC驱动程序。

### 任务1.启用LogMiner

LogMiner提供重做日志，以总结数据库活动。来源使用这些日志来生成记录。

LogMiner需要以ARCHIVELOG模式打开数据库并启用归档。要确定数据库的状态并启用LogMiner，请使用以下步骤：

1. 以具有DBA特权的用户身份登录数据库。

2. 检查数据库日志记录模式：

   ```
   select log_mode from v$database;
   ```

   如果命令返回ARCHIVELOG，则可以跳至[任务2](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_gvh_3c2_3y)。

   如果命令返回NOARCHIVELOG，请继续执行以下步骤：

3. 关闭数据库：

   ```
   shutdown immediate;
   ```

4. 启动并安装数据库：

   ```
   startup mount;
   ```

5. 配置启用存档并打开数据库：

   ```
   alter database archivelog;
   alter database open;
   ```

### 任务2.启用补充日志记录

要从重做日志中检索数据，LogMiner需要对数据库或表进行补充日志记录。

对于要使用的每个表，至少在表级别启用主键或“标识键”日志记录。使用标识键日志记录，记录仅包括主键和更改的字段。

由于Oracle的已知问题，要为表启用补充日志记录，必须首先为数据库启用最小补充日志记录。

要在源生成的记录中包括所有字段，请在表或数据库级别启用完整的补充日志记录。完全补充日志记录可提供来自所有列的数据，这些列具有不变的数据以及主键和已更改的列。有关基于补充日志记录类型的记录中包含的数据的详细信息，请参见 [生成的记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_vfw_bjz_ty)。

1. 要验证是否为数据库启用了补充日志记录，请运行以下命令：

   ```
   SELECT supplemental_log_data_min, supplemental_log_data_pk, supplemental_log_data_all FROM v$database;
   ```

   如果命令对所有三列返回“是”或“隐式”，则同时使用标识密钥和完整补充日志启用补充日志。您可以跳至[任务3。](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_jnz_bd2_3y)

   如果该命令的前两列返回“是”或“隐式”，则使用标识密钥日志记录启用补充日志记录。如果这是您想要的，则可以跳至[任务3](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_jnz_bd2_3y)。

2. 启用识别码或完整的补充日志记录。

   对于12c或18c多租户数据库，最佳实践是为表的容器而不是整个数据库启用日志记录。您可以先使用以下命令将更改仅应用于容器：

   ```
   ALTER SESSION SET CONTAINER=<pdb>;
   ```

   您可以启用标识密钥或完整的补充日志记录，以从重做日志中检索数据。您无需同时启用以下两项：

   - 启用识别键记录

     您可以为数据库中的单个表或所有表启用标识键日志记录：

     对于单个表使用以下命令为数据库启用最少的补充日志记录，然后为要使用的每个表启用标识键日志记录：`ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;``ALTER TABLE . ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;`对于所有桌子使用以下命令为整个数据库启用标识密钥日志记录：`ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;`

   - 启用完整的补充日志记录

     您可以为数据库中的单个表或所有表启用完整的补充日志记录：

     对于单个表使用以下命令为数据库启用最少的补充日志记录，然后为要使用的每个表启用完整的补充日志记录：`ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;``ALTER TABLE . ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;`对于所有桌子使用以下命令为整个数据库启用完整的补充日志记录：`ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;`

3. 提交更改：

   ```
   ALTER SYSTEM SWITCH LOGFILE;
   ```

### 任务3.创建一个用户帐户

创建一个用户帐户以与Oracle CDC客户端源一起使用。您需要该帐户才能通过JDBC访问数据库。

您根据所使用的Oracle版本创建帐户的方式有所不同：

- Oracle 12c或18c多租户数据库

  对于多租户Oracle 12c或18c数据库，请创建一个公共用户帐户。普通用户帐户是在中创建的，`cdb$root`并且必须使用以下约定：`c##`。

  以具有DBA特权的用户身份登录数据库。创建普通用户帐户：`ALTER SESSION SET CONTAINER=cdb$root; CREATE USER  IDENTIFIED BY  CONTAINER=all; GRANT create session, alter session, set container, logmining, execute_catalog_role TO  CONTAINER=all; GRANT select on GV_$DATABASE to ; GRANT select on V_$LOGMNR_CONTENTS to ; GRANT select on GV_$ARCHIVED_LOG to ; ALTER SESSION SET CONTAINER=; GRANT select on . TO ;`对要使用的每个表重复最终命令。

  配置源时，请使用此用户帐户获取JDBC凭据。使用整个用户名（包括 `c##`）作为JDBC用户名。

- Oracle 12c或18c标准数据库

  对于标准的Oracle 12c或18c数据库，创建一个具有必要特权的用户帐户：以具有DBA特权的用户身份登录数据库。创建用户帐户：`CREATE USER  IDENTIFIED BY ; GRANT create session, alter session, logmining, execute_catalog_role TO ; GRANT select on GV_$DATABASE to ; GRANT select on V_$LOGMNR_CONTENTS to ; GRANT select on GV_$ARCHIVED_LOG to ; GRANT select on . TO ;`对要使用的每个表重复最终命令。

  配置源时，请使用此用户帐户获取JDBC凭据。

- Oracle 11g数据库

  对于Oracle 11g数据库，创建一个具有必要特权的用户帐户：以具有DBA特权的用户身份登录数据库。创建用户帐户：`CREATE USER  IDENTIFIED BY ; GRANT create session, alter session, execute_catalog_role, select any transaction, select any table to ; GRANT select on GV_$DATABASE to ; GRANT select on GV_$ARCHIVED_LOG to ; GRANT select on V_$LOGMNR_CONTENTS to ; GRANT select on . TO ; `对要使用的每个表重复最终命令。

  配置源时，请使用此用户帐户获取JDBC凭据。

### 任务4.提取Log Miner词典（重做日志）

使用重做日志作为字典源时，必须在启动管道之前将Log Miner字典提取到重做日志。定期重复此步骤，以确保包含字典的重做日志仍然可用。

Oracle建议您仅在非高峰时间提取字典，因为提取会消耗数据库资源。

要提取Oracle 11g，12c或18c数据库的字典，请运行以下命令：

```
EXECUTE DBMS_LOGMNR_D.BUILD(OPTIONS=> DBMS_LOGMNR_D.STORE_IN_REDO_LOGS);
```

要提取Oracle 12c或18c多租户数据库的字典，请运行以下命令：

```
ALTER SESSION SET CONTAINER=cdb$root;
EXECUTE DBMS_LOGMNR_D.BUILD(OPTIONS=> DBMS_LOGMNR_D.STORE_IN_REDO_LOGS);
```

### 任务5.安装驱动程序

Oracle CDC客户端源通过JDBC连接到Oracle。您必须安装所需的驱动程序才能访问数据库。

**注意：** StreamSets已使用Oracle 11g和Oracle 11.2.0 JDBC驱动程序测试了源。

为您使用的Oracle数据库版本安装Oracle JDBC驱动程序。有关安装其他驱动程序的信息，请参阅 Data Collector 文档 中的“ [安装外部库](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/ExternalLibs.html%23concept_pdv_qlw_ft) ”。

## 架构，表名称和排除模式

配置Oracle CDC客户端源时，可以使用要处理的变更捕获数据来指定表。要指定表，请定义模式，表名称模式和可选的排除模式。

定义架构和表名称模式时，可以使用[正则表达式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-RegEx/RegEx-Title.html#concept_vd4_nsc_gs) 在一个架构内或跨多个架构定义一组表。如果需要，您还可以使用正则表达式作为排除模式来从较大的集合中排除表的子集。

例如，假设您要处理销售模式中以SALES开头的所有表的变更数据捕获数据，而排除以破折号（-）和单字符后缀结尾的表。您可以使用以下配置来指定要处理的表：

- 架构：销售
- 表格名称格式：SALES *
- 排除方式：SALES。*-。

## 初始变更

初始更改是LogMiner重做日志中要开始处理的点。启动管道时，Oracle CDC客户端将从指定的初始更改开始进行处理，并继续进行直到停止管道为止。

请注意，Oracle CDC客户端进程仅更改捕获数据。如果需要现有数据，则可以在启动Oracle CDC客户端管道之前，在单独的管道中使用JDBC查询使用者或JDBC多表使用者来读取表数据。

Oracle CDC客户端提供了几种配置初始更改的方法：

- 从最新变化

  原点处理在启动管道之后发生的所有更改。

- 从指定的日期时间开始

  源处理在指定的日期时间及以后发生的所有更改。使用以下格式：`DD-MM-YYYY HH24:MI:SS`。

- 从指定的系统更改号（SCN）

  源处理指定SCN和更高版本中发生的所有更改。使用指定的SCN时，原点将从与SCN关联的时间戳开始处理。如果在重做日志中找不到SCN，则原点会继续从重做日志中可用的下一个更高的SCN中读取。

  通常，数据库管理员可以提供要使用的SCN。

### 例

您想要处理Orders表中的所有现有数据，然后捕获更改的数据，并将所有数据写入Amazon S3。要读取现有数据，请使用带有JDBC Query Consumer和Amazon S3目标的管道，如下所示：

![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/OracleCDC-JDBCpipe.png)

读取所有现有数据后，您将停止JDBC Query Consumer管道并启动以下Oracle CDC Client管道。该管道被配置为接收在启动管道之后发生的更改，但是如果您想防止任何数据丢失的机会，则可以将初始更改配置为确切的日期时间或更早的SCN：

![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/OracleCDC-pipeline.png)

## 选择缓冲区

处理数据时，Oracle CDC客户端可以在Data Collector 计算机上本地缓冲数据，也可以使用Oracle LogMiner缓冲区：

- 本地缓冲区

  使用本地缓冲区时，源请求有关表和时间段的事务。源将缓冲生成的LogMiner重做SQL语句，直到它验证事务的提交为止。看到提交后，它将解析并处理提交的数据。源可以将重做SQL语句完全缓冲在内存中，也可以将它们主要写入磁盘，同时使用少量内存进行跟踪。默认情况下，原点使用本地缓冲区。通常，使用本地缓冲区应该比Oracle Log Miner缓冲区提供更好的性能。使用本地缓冲区来处理大型事务或避免垄断Oracle PGA。当Data Collector资源允许时，将信息缓冲在内存中以获得更好的性能。将信息缓冲到磁盘上，以避免独占Data Collector资源。

- Oracle LogMiner缓冲区

  使用Oracle LogMiner缓冲区时，源在特定时间段内从Oracle LogMiner请求数据。然后，LogMiner会缓冲该时间段内数据库中所有表的所有事务，而不是仅缓冲源所需要的表。

  LogMiner将事务信息保留在缓冲区中，直到读取日志中的提交，然后将提交的数据传递到Oracle CDC客户端源。

  根据事务的大小和数量以及要缓冲的时间段，对事务进行缓冲可以独占PGA（Oracle服务器进程的专用内存区域）。

  当您不希望事务量使Oracle资源负担过多时，请使用LogMiner缓冲区。

### 本地缓冲区资源要求

在使用本地缓冲区之前，您应该验证分配的资源是否足以满足管道的需求。

根据您使用的本地缓冲选项，使用以下准则：

- 在记忆中

  当在内存中进行缓冲时，源将缓冲Oracle返回的LogMiner重做SQL语句。在收到该语句的提交后，它将处理数据。

  在内存中进行缓冲之前，请增加Data Collector Java堆大小以容纳您期望管道接收的信息。

  使用以下公式估算Java堆大小要求：

  `Required Memory = (L * (30 + S) * T * 1.5) bytes`L-LogMiner为每个事务生成的最大语句数。每一行更改都会生成一个包含所有字段名称和值的语句。S-LogMiner生成的每个重做SQL语句的最大字符长度，包括所有字段名称和值。例如，以下SQL语句包含92个字符，包括空格和标点符号。`insert into "SYS"."Y"("ID","D") values ('690054',TO_TIMESTAMP('2017-07-18 02:00:00.389390'))`T-在任何给定时间，表中正在进行的最大交易数。

  30表示也存储的语句ID中的字节数。

- 到磁盘

  当缓冲到磁盘时，源仅将每个SQL查询的语句ID存储在内存中。然后将查询保存到磁盘。

  请注意，当缓冲到磁盘时，数据收集器会在管道启动和停止时清除缓冲的数据。重新启动管道时，除非您重置原点，否则Data Collector将使用最后保存的偏移量。

  在缓冲到磁盘之前，请确保本地磁盘上有足够的可用空间。您可能还会增加Data Collector Java堆的大小，但程度要小于在内存中完全缓冲时的程度。

  使用以下计算来确定管道所需的磁盘空间量：`Required Disk Space = (L * S * T * 1.5) bytes`L-LogMiner为每个事务生成的最大语句数。每一行更改都会生成一个包含所有字段名称和值的语句。S-LogMiner生成的每个重做SQL语句的最大字符长度，包括所有字段名称和值。例如，以下SQL语句包含92个字符，包括空格和标点符号。`insert into "SYS"."Y"("ID","D") values ('690054',TO_TIMESTAMP('2017-07-18 02:00:00.389390'))`T-在任何给定时间，表中正在进行的最大交易数。

  使用以下方程式估算管道所需的Java堆大小：

  `Required Memory = (L * T * 30 * 1.5) bytes`L-LogMiner为每个事务生成的最大语句数。每一行更改都会生成一个包含所有字段名称和值的语句。T：在任何给定时间，表中正在运行的最大事务数。

有关配置Data Collector堆大小的信息，请参阅Data Collector 文档中的[Java Heap Size](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html%23concept_mdc_shg_qr)。

### 未提交的交易处理

使用本地缓冲区时，可以配置Oracle CDC客户端源如何处理旧的未提交的事务。旧交易是早于当前交易窗口的交易。

默认情况下，源处理旧的已提交事务。它将每个LogMiner重做SQL语句转换为一条记录，并将该记录传递给阶段以进行错误处理。

如果不需要错误记录，则可以将源配置为丢弃未提交的事务。这也减少了用于生成错误记录的开销。

## 包含空值

当Oracle LogMiner执行完整的补充日志记录时，结果数据将包括表中的所有列，这些列的值为零，并且其中未发生任何更改。默认情况下，当Oracle CDC客户端处理此数据时，它会在生成记录时忽略空值。

您可以配置原点以在记录中包括空值。当目标系统具有必填字段时，可能需要包括空值。要包括空值，请在Oracle CDC选项卡上启用“包括空值”属性。

## 不支持的数据类型

您可以配置来源处理包含不支持的数据类型的记录的方式。原点可以执行以下操作：

- 将记录传递到没有不支持的数据类型的管道中。
- 将记录传递给错误，而不包含不支持的数据类型。
- 丢弃记录。

您可以配置源，以在记录中包括不受支持的数据类型。当您包含不受支持的类型时，原点将包括字段名称，并将数据作为未解析的字符串进行传递。

Oracle CDC客户端来源不支持以下Oracle数据类型：

- 数组
- 斑点
- b
- 数据链接
- 不同
- 间隔
- Java对象
- Nclob
- 其他
- 参考
- 参考光标
- SQLXML
- 结构
- 时区时间

### 条件数据类型支持

请注意有关有条件支持的Oracle数据类型的以下信息：

- Oracle Raw数据类型被视为数据收集器字节数组。
- 具有Timezone数据类型的Oracle Timestamp将转换为Data Collector分区的Datetime数据类型。为了在提高效率的同时提供更高的精度，原点仅包含数据的UTC偏移量。它省略了时区ID。

## 生成的记录

将源配置为解析SQL查询后，它会根据Oplog操作类型以及为数据库和表启用的日志记录，以不同的方式生成记录。它还在记录头属性中包括CDC和CRUD信息。

下表描述了来源如何生成记录数据：

| Oplog操作          | 仅标识/主键记录                            | 完整的补充记录 |
| :----------------- | :----------------------------------------- | :------------- |
| 插入               | 包含数据的所有字段，而忽略具有空值的字段。 | 所有领域。     |
| 更新               | 主键字段和具有更新值的字段。               | 所有领域。     |
| SELECT_FOR_ UPDATE | 主键字段和具有更新值的字段。               | 所有领域。     |
| 删除               | 主键字段。                                 | 所有领域。     |

当原始配置为不解析SQL查询时，它仅将每个LogMiner SQL语句写入SQL字段。

### CRUD操作标头属性

将Oracle CDC客户端配置为解析SQL查询时，它将在生成记录时在以下两个记录头属性中指定操作类型：

- sdc.operation.type

  Oracle CDC客户端会评估与其处理的每个条目关联的Oplog操作类型，并在适当时将操作类型写入sdc.operation.type记录头属性。

  原点在sdc.operation.type记录头属性中使用以下值表示操作类型：INSERT为12个代表删除3用于UPDATE和SELECT_FOR_UPDATE

  如果您在诸如JDBC Producer或Elasticsearch之类的管道中使用启用CRUD的目标，则该目标可以在写入目标系统时使用操作类型。必要时，可以使用表达式评估器或脚本处理器来处理`sdc.operation.type`header属性中的值 。有关Data Collector更改的数据处理的概述以及启用CRUD的目标的列表，请参阅[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

  使用启用了CRUD的目标时，目标在检查oracle.cdc.operation属性之前会在sdc.operation.type属性中查找操作类型。

- oracle.cdc.operation

  Oracle CDC客户端还将Oplog CRUD操作类型写入oracle.cdc.operation记录标头属性。此属性是在较早的版本中实现的，并且为了向后兼容而受支持。

  源将Oplog操作类型作为以下字符串写入oracle.cdc.operation属性：插入更新SELECT_FOR_ UPDATE删除启用CRUD的目标在检查sdc.operation.type属性后，会为该操作类型检查此属性。

### CDC标头属性

将Oracle CDC客户端配置为解析SQL查询时，它将为每个记录提供以下CDC记录头属性：

- oracle.cdc.operation
- oracle.cdc.query
- oracle.cdc.rowId
- oracle.cdc.scn
- oracle.cdc.timestamp
- oracle.cdc.table
- oracle.cdc.user

并且它为记录中的每个十进制字段包括以下记录头属性：

- jdbc。<字段名称> .precision
- jdbc。<字段名称> .scale

您可以使用`record:attribute`或 `record:attributeOrDefault`函数来访问属性中的信息。有关使用记录标题属性的更多信息，请参见[使用标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_rd2_ghz_dz)。

## 事件产生

当Oracle CDC客户端源将重做日志用作字典源时，可以生成事件流中可以使用的事件。当使用在线目录作为字典源时，原点不会生成事件。

当您将重做日志用作字典源并启用事件生成时，Oracle CDC客户端在读取DDL语句时会生成事件。它为ALTER，CREATE，DROP和TRUNCATE语句生成事件。

启动管道时，原始服务器查询数据库并为原始服务器中列出的所有表缓存模式，然后为每个表生成初始事件记录。每个事件记录描述每个表的当前架构。在处理与数据相关的重做日志条目时，源使用缓存的架构生成记录。

然后，源将为其在重做日志中遇到的每个DDL语句生成一个事件记录。每个事件记录在记录头属性中都包含DDL语句和相关表。

源在事件记录中包括新表和更新表的表架构信息。当源遇到ALTER或CREATE语句时，它将查询数据库以获取表的最新架构。

如果ALTER语句是对缓存模式的更新，则源将更新缓存并将更新的表模式包括在事件记录中。如果ALTER语句早于缓存的架构，则源不将表架构包括在事件记录中。同样，如果CREATE语句用于“新”表，则源将缓存新表，并将表架构包括在事件记录中。因为源在管道启动时会验证所有指定的表都存在，所以只有在管道启动后删除并创建表时，才会发生这种情况。如果CREATE语句用于已缓存的表，则源不将表架构包括在事件记录中。

Oracle CDC客户端事件可以任何逻辑方式使用。例如：

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

Oracle CDC客户端来源生成的事件记录具有以下与事件相关的记录头属性。记录头属性存储为字符串：

| 记录标题属性                 | 描述                                             |
| :--------------------------- | :----------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下类型之一：启动改变创建下降截短 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                   |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                         |
| oracle.cdc.table             | 更改的Oracle数据库表的名称。                     |
| oracle.cdc.ddl               | 触发事件的DDL语句。                              |

当管道启动时，将生成STARTUP事件记录。源为每个表创建一个事件记录。事件记录包括当前表架构。

DROP和TRUNCATE事件记录仅包括上面列出的记录头属性。

当删除并重新创建表时，CREATE事件记录包括新表的架构。语句更新缓存的架构时，ALTER事件记录包括表架构。有关CREATE和ALTER语句的行为的更多信息，请参见[事件生成](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_h2t_hx1_vy)。

例如，以下ALTER事件记录在更新的架构中显示三个字段-NICK，ID和NAME：

![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/OracleCDC-eventrecord.png)

在记录标题属性列表中，请注意添加了NICK字段的DDL语句，更新的表的名称和ALTER事件类型。

## PEG解析器（测试版）

Oracle CDC客户端源提供了备用的PEG解析器，您在担心管道性能时可以尝试使用。

Oracle CDC客户端源提供了PEG解析器，作为默认解析器的替代。当您认为默认解析器的性能不足时（例如处理非常宽的表时），可以尝试使用PEG解析器。请注意，PEG解析器目前处于beta状态，在用于生产之前应仔细测试。

有关PEG处理的更多信息，请参见https://en.wikipedia.org/wiki/Parsing_expression_grammar。

要使用PEG解析器，请在Oracle CDC选项卡上启用Parse SQL Query属性，然后在“高级”选项卡上启用Use PEG Parser属性。

## 多线程解析

在将源配置为使用本地缓冲并解析SQL查询时，可以将Oracle CDC客户端源配置为使用多个线程来解析事务。您可以将多线程解析与默认的Oracle CDC客户端解析器和备用PEG解析器一起使用。

执行多线程分析时，源使用多个线程从事务中已提交的SQL语句生成记录。它不对结果记录执行多线程处理。

Oracle CDC客户端源基于“解析线程池大小”属性使用多个线程进行解析。启动管道时，源将创建属性中指定的线程数。源服务器连接到Oracle，创建LogMiner会话，并一次处理一个事务。

当源处理事务时，它将读取事务中的所有SQL语句并将其缓冲到内存中的队列中，并在处理语句之前等待语句被提交。提交后，将使用所有可用线程来解析SQL语句，并保留SQL语句的原始顺序。

结果记录将传递到管道的其余部分。请注意，启用多线程分析不会启用多线程处理–管道使用单个线程读取数据。

## 使用SQL解析器处理器

对于非常宽的表（具有几百列的表），使用Oracle CDC Client源读取重做日志并解析SQL查询可能会花费比预期更长的时间，并且可能导致Oracle重做日志在被读取之前被转出。发生这种情况时，数据将丢失。

为避免这种情况，可以使用多个管道和SQL Parser处理器。第一个管道包含Oracle CDC客户端和一个中间端点。将源配置为不解析SQL查询。第二个管道将记录从中间端点传递到SQL Parser处理器，以解析SQL查询并更新字段。使用这种方法，源服务器可以读取重做日志，而无需等待SQL解析器完成，因此不会丢失任何数据。

Oracle CDC客户端继续执行以下任务：

- 读取更改数据日志。
- 生成仅包含SQL查询的记录。
- 生成事件。

SQL Parser处理器执行以下任务：

- 解析SQL查询。
- 生成CDC和CRUD记录头属性。

有关SQL Parser处理器的更多信息，请参见[SQL Parser](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/SQLParser.html#concept_zh2_kfj_tdb)。

## 使用漂移同步解决方案

如果将Oracle CDC客户端起源用作Hive管道漂移同步解决方案的一部分，请确保仅将标记为“插入”的记录传递给Hive元数据处理器。

用于Hive的Drift同步解决方案可根据传入数据自动更新Hive表。Hive元数据处理器是解决方案的第一阶段，它只希望插入记录。以下是一些推荐方法，可确保处理器仅接收插入记录：

- 将Oracle CDC客户端配置为仅处理插入记录。
- 如果要在管道中处理其他记录类型，请使用流选择器将仅“插入”记录路由到Hive元数据处理器。

**相关概念**

[蜂巢漂移同步解决方案](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Hive_Drift_Solution/HiveDriftSolution_title.html#concept_phk_bdf_2w)

## 使用Oracle CDC客户端进行数据预览

将数据预览与Oracle CDC Client源一起使用时，可能需要增加“预览超时”数据预览属性。

默认情况下，数据预览在超时之前会等待10,000毫秒（10秒）来建立与原始系统的连接。由于此来源的复杂性，初始启动可能比默认启动花费更长的时间。

如果数据预览超时，请尝试将超时属性增加到120,000毫秒。如果预览继续超时，请逐渐增加超时。

## 配置Oracle CDC客户端

配置Oracle CDC客户端源，以处理来自Oracle数据库的LogMiner更改数据捕获信息。

使用原点之前，请完成先决条件任务。有关更多信息，请参见 [Oracle CDC客户端先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_xwg_33w_cx)。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_h2t_hx1_vy) | 当原始服务器将重做日志用作字典源时，可以在原始服务器读取DDL语句时生成事件记录。用于[事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **Oracle CDC”**选项卡上，配置以下更改数据捕获属性：

   | Oracle CDC属性                                               | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [桌子](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_gj4_sjq_qcb) | 跟踪表。根据需要指定相关属性。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以定义另一个表配置。 |
   | 模式名称                                                     | 使用的架构。您可以输入架构名称或使用[SQL LIKE语法](https://docs.oracle.com/database/121/SQLRF/conditions007.htm#SQLRF00501)指定一组架构。默认情况下，源以大写形式提交架构名称。要使用小写或大小写混合的名称，请选择“区分大小写的名称”属性。 |
   | [表名称模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_gj4_sjq_qcb) | 表名称模式，用于指定要跟踪的表。您可以输入表名或使用[SQL LIKE语法](https://docs.oracle.com/database/121/SQLRF/conditions007.htm#SQLRF00501)指定一组表。默认情况下，来源会以全部大写形式提交表格名称。若要使用小写或大小写混合的名称，请选择“区分大小写的名称”属性。 |
   | [排除方式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_gj4_sjq_qcb) | 可选的表排除模式，用于定义要排除的表子集。您可以输入表名或使用[正则表达式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-RegEx/RegEx-Title.html#concept_vd4_nsc_gs)指定要排除的表的子集。 |
   | 区分大小写的名称                                             | 提交区分大小写的架构，表和列名称。如果未选择，则起源会以大写形式提交名称。默认情况下，Oracle使用所有大写字母表示架构，表和列的名称。仅当使用名称周围的引号创建模式，表或列时，名称才可以是小写或大小写混合。 |
   | [初始变更](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_zrc_pyj_dx) | 读取的起点。使用以下选项之一：来自最新更改-处理在启动管道之后到达的更改。从日期开始-处理从指定日期开始的更改。从SCN-从指定的系统更改号（SCN）开始处理更改。 |
   | 开始日期                                                     | 从启动管道开始读取的日期时间。对于基于日期的初始更改。使用以下格式：`DD-MM-YYYY HH24:MI:SS`。 |
   | 启动SCN                                                      | 启动管道时从其开始读取的系统更改号。如果在重做日志中找不到SCN，则原点会继续从重做日志中可用的下一个更高的SCN中读取。对于基于SCN的初始更改。 |
   | 运作方式                                                     | 创建记录时要包括的操作。所有未列出的操作都将被忽略。         |
   | [字典来源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_eq5_wh2_dx) | LogMiner词典的位置：重做日志-架构可以更改时使用。允许原点适应模式更改并为DDL语句生成事件。联机目录-当不希望更改架构时，可用于提高性能。 |
   | [本地缓冲区更改](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_yqk_3hn_n1b) | 确定原点使用的缓冲区。选择以使用本地Data Collector缓冲区。清除以使用Oracle LogMiner缓冲区。通常，使用本地缓冲区将提高管道性能。默认情况下，原点使用本地缓冲区。 |
   | 缓冲区位置                                                   | 本地缓冲区的位置：在记忆中到磁盘在运行管道之前，请注意本地缓冲区资源要求。有关更多信息，请参见[本地缓冲区资源要求](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_atl_ytf_p1b)。 |
   | [放弃旧的未提交的交易](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_ssg_tgm_p1b) | 丢弃较早的未提交事务，而不是将它们处理为错误记录。           |
   | 不支持的字段类型                                             | 当源在记录中遇到不支持的数据类型时采取的操作：忽略记录并将其发送到管道-源将忽略不支持的数据类型，并将仅具有支持的数据类型的记录传递到管道。将记录发送到错误-源根据为该阶段配置的错误记录处理该记录。错误记录仅包含支持的数据类型。丢弃记录-来源丢弃记录。有关不支持的数据类型的列表，请参见[不支持的数据类型](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_gwp_d4n_n1b)。 |
   | 将不支持的字段添加到记录                                     | 在记录中包括具有不受支持的数据类型的字段。包括字段名称和不支持的字段的未解析的字符串值。 |
   | 包含空值                                                     | 在通过完全补充日志记录生成的记录中包括空值，其中包括空值。默认情况下，原点会生成没有空值的记录。 |
   | 将时间戳转换为字符串                                         | 使原点能够将时间戳记写为字符串值而不是日期时间值。字符串保持存储在源数据库中的精度。例如，字符串可以保持高精度Timestamp（6）字段的精度。在将时间戳写入不存储纳秒的Data Collector日期或时间数据类型时，原点会将距时间戳的任何纳秒存储在[field属性中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)。 |
   | 最大交易时长                                                 | 等待更改的时间（以秒为单位）。输入您期望交易所需的最长时间。默认值为$ {1 * HOURS}，即3600秒。 |
   | LogMiner会话窗口                                             | 保持LogMiner会话打开的时间（以秒为单位）。设置为大于最大事务长度。不使用本地缓冲时减少以减少LogMiner资源的使用。默认值为$ {2 * HOURS}，即7200秒。 |
   | 解析SQL查询                                                  | 解析SQL查询。如果设置为false，则源将SQL查询写入一个`sql`字段，该字段稍后可以由[SQL Parser处理器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/SQLParser.html#concept_zh2_kfj_tdb)解析。缺省值为true，表示源解析SQL查询。 |
   | 发送标头中的重做查询                                         | 在`oracle.cdc.query`记录标题属性中包括LogMiner重做查询 。    |
   | 数据库时区                                                   | 数据库的时区。指定数据库何时在不同于Data Collector的时区中运行。 |

3. 在“ **JDBC”**选项卡上，配置以下JDBC属性：

   | JDBC属性             | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | JDBC连接字符串       | 用于连接数据库的连接字符串。**注意：**如果在连接字符串中包括JDBC凭据，请使用为源创建的用户帐户。Oracle 12c或18c多租户数据库的普通用户帐户以开头`c##`。有关更多信息，请参见[任务3。创建用户帐户](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_jnz_bd2_3y)。 |
   | 最大批次大小（记录） | 一次处理的最大记录数。接受的值最高为Data Collector的最大批处理大小。默认值是1000 数据采集器默认设置为1000。 |
   | PDB                  | 包含要使用的架构的可插拔数据库的名称。仅在可插拔数据库中创建架构时使用。在可插入数据库中创建的架构是必需的。 |
   | 使用凭证             | 在“凭据”选项卡上启用输入凭据。在JDBC连接字符串中不包括凭据时使用。 |
   | 其他JDBC配置属性     | 要使用的其他JDBC配置属性。要添加属性，请单击 **添加**并定义JDBC属性名称和值。使用JDBC期望的属性名称和值。 |

4. 要与JDBC连接字符串分开输入JDBC凭据，请在“ **凭据”**选项卡上配置以下属性：

   | 凭证属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 用户名   | JDBC连接的用户名。使用为原点创建的用户帐户。Oracle 12c或18c多租户数据库的普通用户帐户以开头 `c##`。有关更多信息，请参见[任务3。创建用户帐户](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_jnz_bd2_3y)。 |
   | 密码     | 帐户密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |

5. 使用低于4.0的JDBC版本时，在“ **旧版驱动程序”**选项卡上，可以选择配置以下属性：

   | 旧版驱动程序属性     | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | JDBC类驱动程序名称   | JDBC驱动程序的类名。早于版本4.0的JDBC版本必需。              |
   | 连接运行状况测试查询 | 可选查询，用于测试连接的运行状况。仅当JDBC版本低于4.0时才建议使用。 |

6. 在“ **高级”**选项卡上，可以选择配置高级属性。

   这些属性的默认值在大多数情况下都应该起作用：

   | 先进物业                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 当前窗口的JDBC提取大小                                       | 当LogMiner会话包含当前时间时，要一起提取和处理的重做日志数。在会话结束时，源将获取并处理所有剩余的重做日志。设置为1可使数据在原点收到重做日志后立即可用。默认值为1。 |
   | 过去Windows的JDBC提取大小                                    | LogMiner会话完全过去时要一起获取和处理的重做日志数。当获取大小超过可用重做日志时，源将获取并处理剩余的重做日志。当对目标系统的写入速度较慢时，较小的访存大小可以提高吞吐量。默认值为1。 |
   | [使用PEG分析器（测试版）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_jy5_dyd_12b) | 启用使用beta PEG解析器，而不使用默认的Oracle CDC Client原始解析器。可以提高性能，但在生产中使用前应仔细测试。 |
   | [解析线程池大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_syx_pjy_12b) | 原点用于多线程解析的线程数。仅在执行本地缓冲和解析SQL查询时可用。可以与默认解析器和PEG解析器一起使用。 |
   | 最大游泳池                                                   | 创建的最大连接数。默认值为1。建议值为1。                     |
   | 最小空闲连接                                                 | 创建和维护的最小连接数。要定义固定连接池，请设置为与“最大池大小”相同的值。默认值为1。 |
   | 连接超时                                                     | 等待连接的最长时间。在表达式中使用时间常数来定义时间增量。默认值为30秒，定义如下：`${30 * SECONDS}` |
   | 空闲超时                                                     | 允许连接空闲的最长时间。在表达式中使用时间常数来定义时间增量。使用0以避免删除任何空闲连接。当输入的值接近或超过连接的最大生存期时，Data Collector将忽略空闲超时。默认值为10分钟，定义如下：`${10 * MINUTES}` |
   | 最大连接寿命                                                 | 连接的最大寿命。在表达式中使用时间常数来定义时间增量。使用0设置最大寿命。设置最大寿命时，最小有效值为30分钟。默认值为30分钟，定义如下：`${30 * MINUTES}` |
   | 强制执行只读连接                                             | 创建只读连接以避免任何类型的写入。默认启用。不建议禁用此属性。 |
   | 交易隔离                                                     | 用于连接数据库的事务隔离级别。默认是为数据库设置的默认事务隔离级别。您可以通过将级别设置为以下任意值来覆盖数据库默认值：阅读已提交阅读未提交可重复读可序列化 |