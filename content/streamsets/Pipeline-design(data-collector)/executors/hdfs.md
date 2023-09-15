# HDFS文件元数据执行器

每次接收事件时，HDFS文件元数据执行程序都会更改文件元数据，创建一个空文件或删除HDFS或本地文件系统中的文件或目录。您不能在同一执行程序中执行多个任务。要执行多个任务，请使用其他执行程序。

将HDFS文件元数据执行程序用作事件流的一部分。例如，在执行程序从Hadoop FS目标接收到文件关闭事件后，您可以使用执行程序来移动文件或更改文件权限。

您可以以任何逻辑方式使用执行程序，例如在从Hadoop FS或本地FS目标接收文件关闭事件后更改文件元数据。

更改元数据时，可以配置一个表示要处理的文件的位置和名称的表达式，然后指定要执行的更改。创建空文件时，可以指定文件的输出位置，还可以选择指定文件的所有者，权限和ACL。删除文件或目录时，可以指定文件或目录的位置。

必要时，可以启用Kerberos身份验证并指定HDFS用户。您还可以使用HDFS配置文件，并根据需要添加其他HDFS配置属性。

您还可以配置执行程序以为另一个事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

有关使用HDFS文件元数据执行程序的[案例研究](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_d1q_xl4_lx)，请参阅[案例研究：输出文件管理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_d1q_xl4_lx)。

## 相关事件产生阶段

在管道的事件流中使用HDFS文件元数据执行程序。尽管您可以以任何逻辑方式使用执行程序，但是HDFS文件元数据执行程序经过优化，可以更新以下阶段编写的输出文件或整个文件的文件元数据：

- Hadoop FS目标
- 本地FS目的地

## 更改元数据

您可以配置HDFS文件元数据执行程序，以在收到事件后更改HDFS或本地文件系统中文件的元数据。例如，您可以使用执行程序在目标关闭文件后更改文件许可权。

更改文件元数据时，HDFS文件元数据执行程序可以同时更改以下文件元数据：

- 文件名
- 档案位置
- 文件所有者和组
- 档案权限
- 访问控制列表（ACL）

更改元数据时，模拟HDFS用户的用户必须具有执行任务所需的权限。有关HDFS用户的更多信息，请参见[HDFS用户](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HDFSMetadata.html#concept_krb_g2f_pz)。

### 指定文件路径

使用HDFS文件元数据执行程序更改文件元数据时，请为“文件路径”属性指定一个表达式，该表达式提供要使用的文件的绝对路径。

使用默认文件路径表达式`${record:value('/filepath')}`来更新由Hadoop FS或Local FS目标关闭的输出文件。这些目标生成的文件关闭事件记录包括一个文件路径字段，其中包含关闭的输出文件的位置和名称。

要更新Hadoop FS或Local FS目标已完成流传输的整个文件，请使用以下表达式：

```
${record:value('/targetFileInfo/path')}
```

来自这些目标的整个文件处理的事件记录包括一个/ targetFileInfo / path字段，其中包含已处理的整个文件的位置和名称。

有关目标生成的事件记录的更多信息，请参阅目标文档中的“事件记录”。

### 更改文件名或位置

使用HDFS文件元数据执行程序更改文件元数据时，可以在文件关闭后更改其名称或位置。指定新的文件名和位置时，可以输入常量或表达式。使用任何求值为您要使用的值的表达式。

需要时，可以在表达式中使用文件函数来使用现有文件路径的一部分。文件功能可以返回路径，文件名或扩展名的任何部分。

- 移动文件示例

  假设您有Hadoop FS将JSON文件写入以下目录结构：`/server1/weblogs//`

  写入文件后，您希望HDFS File Metadata executor将文件移动到其他根目录。移动文件时，您需要指定文件的新位置。因此，您可以将执行程序配置为将文件移动到其他目录，同时仍然使用文件的其余路径，如下所示：`/newDir/${file:pathElement(record:value('/filepath'),1)}/${file:pathElement(record:value('/filepath'),2)}/`

  此表达式使用newDir作为新的根目录，然后使用两个级别的子目录。移动文件时不要包括文件名。

- 重命名文件的示例

  假设您要将.json后缀添加到原始文件名。重命名文件时，需要为文件指定新名称，因此请使用以下表达式：`${file:fileName(record:value('/filepath'))}.json`

  该表达式从事件记录中的filepath字段返回文件名，并将.json添加到文件名，例如` .json`。

  如果要从写入文件中删除扩展名，则可以在“新名称”字段中使用以下表达式：`${file:removeExtension(file:fileName(record:value('/filepath')))}`

  此表达式从文件路径事件记录字段返回文件名，然后从名称中除去扩展名，并将结果用作新文件名。

有关文件功能的更多信息，请参见[文件功能](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/Functions.html#concept_kxj_nyl_5x)。

### 定义所有者，组，权限和ACL

使用HDFS文件元数据执行程序更改文件元数据或创建空文件时，可以定义文件所有者，组，文件权限和访问控制列表（ACL）。

**重要说明：**执行程序更改权限时，将删除现有权限并实现请求的权限。执行程序不会将权限添加到现有权限中，因此请确保完全按照所需的方式配置权限。

您可以使用以下方法的任意组合设置权限：

- 定义新的所有者和组

  您可以定义文件的所有者和组。使用此选项时，必须同时输入所有者和组名。

- 使用八进制或符号格式设置文件权限

  您可以通过输入要使用八进制或符号格式的权限来设置文件权限。

  例如，您可以使用以下八进制格式将文件设置为只读：`0444`您也可以使用以下符号格式将文件设置为只读：`-r--r--r-- `

  要使它们对于用户和组为只读状态，禁止所有其他用户访问，则可以使用以下两种格式之一：`0440 -r--r-----`

- 定义ACL

  您可以定义文件的ACL。定义ACL时，请注意HDFS需要为用户，组和其他定义权限。您也可以为其他用户或组添加权限。

  使用以下格式定义ACL：`user::,group::,other::\ [,:]`

  使用符号格式定义权限，其中r，w，x或-代表权限类型。

  例如，以下ACL仅允许用户和组的读取访问权限：`user::r--,group::r--,other::–-`

  如果要允许除与文件关联的组之外的其他访问权限，请输入以下权限：`user::r--,group::r--,other::–-,group:operations:r--`

## 创建一个空文件

您可以将HDFS文件元数据执行程序配置为在收到事件后在HDFS或本地文件系统中创建空文件。您可能会创建空文件来触发其他应用程序（例如Oozie）中的下游操作。

若要创建一个空文件，请为“文件路径”属性指定一个表达式，该表达式提供指向要在其中创建文件的位置的绝对路径。

**注意：**在大多数情况下，您将不想使用默认表达式。默认表达式更适合于更改文件元数据。

创建空文件时，还可以指定以下文件详细信息：

- 文件所有者和组
- 档案权限
- 访问控制列表（ACL）

有关更多信息，请参见[定义所有者，组，权限和ACL](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HDFSMetadata.html#concept_zr1_5ck_rx)。

## 删除文件或目录

您可以配置HDFS文件元数据执行程序，以在收到事件后从HDFS或本地文件系统中删除文件或目录。

例如，假设您每天运行一条将数据写入HDFS的管道。在管道开始处理数据之前，可以使用HDFS文件元数据执行程序删除目标目录及其所有内容。只需配置管道，即可将管道启动事件传递给HDFS文件元数据执行程序，然后在配置执行程序时指定目标目录。有关使用管道事件的更多信息，请参见[管道事件生成](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_amg_2qr_t1b)。

小心删除目录。执行程序以递归方式删除目录，除指定目录外，还删除所有子目录及其内容。

## 事件产生

HDFS文件元数据执行程序可以生成可在事件流中使用的事件。启用事件生成后，执行程序每次更改文件元数据，创建空文件或删除文件或目录时都会生成事件。

HDFS文件元数据事件可以任何逻辑方式使用。例如：

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

HDFS文件元数据执行程序生成的事件记录具有以下与事件相关的记录头属性。记录标题属性存储为字符串值：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下事件类型：文件更改-执行程序更改文件元数据时生成，包括文件名，位置，权限或ACL。file-created-执行程序创建一个空文件时生成。文件删除-在执行程序删除文件或目录时生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

HDFS文件元数据执行程序可以生成以下类型的事件记录：

- 文件已更改

  执行程序在更改文件元数据（包括文件名，位置，权限或ACL）时会生成文件更改事件记录。文件更改的事件记录的sdc.event.type记录标头属性设置为文件更改的，并且包含以下字段：活动栏位名称描述文件路径更改文件的最新路径和名称。文件名更改文件的最新名称。

- 文件已创建

  执行程序在创建一个空文件时会生成一个文件创建的事件记录。文件创建的事件记录的sdc.event.type记录头属性设置为文件创建的，并包含以下字段：活动栏位名称描述文件路径文件创建的位置。文件名文件名。

- 档案已移除

  执行程序在删除文件或目录时会生成一个文件删除事件记录。已删除文件的事件记录的sdc.event.type记录头属性设置为已删除文件，并包括以下字段：活动栏位名称描述文件路径被删除目录的位置，或被删除文件所在的目录。文件名删除的文件名（如果适用）。

## Kerberos身份验证

您可以使用Kerberos身份验证连接到外部系统。使用Kerberos身份验证时，数据收集器将使用Kerberos主体和密钥表进行连接。默认情况下，Data Collector 使用启动它的用户帐户进行连接。

Kerberos主体和密钥表在Data Collector 配置文件中定义`$SDC_CONF/sdc.properties`。要使用Kerberos身份验证，请在数据收集器 配置文件中配置所有Kerberos属性，然后在HDFS文件元数据执行程序中启用Kerberos。

有关为Data Collector启用Kerberos身份验证的详细信息，请参阅Data Collector文档中的[Kerberos身份验证](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_hnm_n4l_xs)。

## HDFS用户

Data Collector 可以使用当前登录的Data Collector用户或在 执行程序中配置的用户来更改文件元数据，创建文件或删除HDFS或本地文件系统中的文件或目录。

可以设置需要使用当前登录的Data Collector用户的Data Collector配置属性 。如果未设置此属性，则可以在源中指定一个用户。有关Hadoop模拟和Data Collector属性的更多信息，请参阅Data Collector文档中的[Hadoop Impersonation Mode](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_pmr_sy5_nz)。

请注意，执行程序使用其他用户帐户进行连接。默认情况下，Data Collector使用启动它的用户帐户连接到外部系统。使用Kerberos时，Data Collector使用Kerberos主体。

要在执行程序中配置用户，请执行以下任务：

1. 在外部系统上，将用户配置为代理用户，并授权该用户模拟HDFS用户。

   有关更多信息，请参见HDFS文档。

2. 在“ HDFS文件元数据”执行器中，输入HDFS用户名。

## HDFS属性和配置文件

您可以将HDFS文件元数据执行程序配置为使用HDFS配置文件和各个HDFS属性：

- HDFS配置文件

  您可以将以下HDFS配置文件与HDFS文件元数据执行程序一起使用：core-site.xmlhdfs-site.xml

  要使用HDFS配置文件：将文件或指向文件的符号链接存储在Data Collector资源目录中。在“ HDFS文件元数据”执行器中，指定文件的位置。**注意：** 对于Cloudera Manager安装，Data Collector会自动创建一个名为的文件的符号链接 `hadoop-conf`。输入`hadoop-conf`HDFS文件元数据执行程序中文件的位置。

- 个别属性

  您可以在执行程序中配置各个HDFS属性。要添加HDFS属性，请指定确切的属性名称和值。HDFS文件元数据执行程序不会验证属性名称或值。**注意：**各个属性会覆盖HDFS配置文件中定义的属性。

## 配置HDFS文件元数据执行器

配置HDFS文件元数据执行程序以在接收到事件后创建一个空文件，更改文件元数据或从HDFS或本地文件系统中删除文件或目录。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 您要使用的库版本。                                           |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HDFSMetadata.html#concept_vhl_mfj_rx) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击**添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |

2. 在“ **HDFS”**选项卡上，配置以下属性：

   | HDFS属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | Hadoop FS URI                                                | 用于访问文件的URI：要访问HDFS中的文件，请输入要使用的HDFS URI。要访问本地目录中的文件，请输入：`file:///` |
   | [HDFS用户](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HDFSMetadata.html#concept_krb_g2f_pz) | HDFS用户，用于在外部系统中创建空文件或更改文件元数据。使用此属性时，请确保正确配置了外部系统。未配置时，管道将使用当前登录的Data Collector用户。将Data Collector配置为使用当前登录的Data Collector用户时，不可配置。有关更多信息，请参阅Data Collector 文档 中的[Hadoop模拟模式](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_pmr_sy5_nz)。 |
   | [Kerberos身份验证](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HDFSMetadata.html#concept_wjp_3hs_rx) | 使用Kerberos凭据连接到外部系统。选中后，将使用在数据收集器配置文件 $ SDC_CONF / sdc.properties中定义的Kerberos主体和密钥表。 |
   | [Hadoop FS配置目录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HDFSMetadata.html#concept_egg_c3s_rx) | HDFS配置文件的位置。对于Cloudera Manager安装，请输入 hadoop-conf。对于所有其他安装，请使用Data Collector资源目录中的目录或符号链接。您可以将以下文件与HDFS文件元数据执行程序一起使用：core-site.xmlhdfs-site.xml**注意：**配置文件中的属性被阶段中定义的单个属性覆盖。 |
   | Hadoop FS配置                                                | 要使用的其他HDFS属性。要添加属性，请单击**添加**并定义属性名称和值。使用外部系统所期望的属性名称和值。 |

3. 在“ **任务”**选项卡上，配置以下属性：

   | 任务属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 任务     | 确定执行者执行的任务类型。您可以创建一个空文件，更改文件元数据或删除文件或目录。要执行多种类型的任务，请将其他执行程序添加到管道中。 |

4. 要创建一个空文件，请配置以下属性：

   | 任务属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 文件路径                                                     | 表示要创建的文件的完整路径的表达式。默认情况下，该属性使用 `${record:value('/filepath')}`。**注意：**在大多数情况下，您将不想使用默认表达式。默认表达式更适合于更改文件元数据。 |
   | [集合所有权](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HDFSMetadata.html#concept_zr1_5ck_rx) | 选择以指定文件所有者或组。                                   |
   | 新主人                                                       | 成为文件新所有者的用户名。                                   |
   | 新组                                                         | 该组将成为文件的新组所有者。                                 |
   | [设置权限](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HDFSMetadata.html#concept_zr1_5ck_rx) | 选择以八进制或符号格式设置文件权限。                         |
   | 新权限                                                       | 八进制或符号格式的文件权限。                                 |
   | [设定ACL](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HDFSMetadata.html#concept_zr1_5ck_rx) | 选择以定义访问控制列表（ACL）权限。                          |
   | 新的ACL                                                      | 为所有者，组和其他定义ACL。您可以选择定义其他用户和组权限。有关详细信息，请参阅[定义所有者，组，权限和ACL](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HDFSMetadata.html#concept_zr1_5ck_rx)。 |

5. 要更改文件元数据，请配置以下属性：

   | 任务属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [文件路径](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HDFSMetadata.html#concept_fmb_rbj_rx) | 表示文件完整路径的表达式。默认情况下，该属性使用 `${record:value('/filepath')}`来处理文件路径字段中的数据。Hadoop FS和Local FS目标都生成文件关闭事件记录，这些记录在filepath字段中包括关闭文件的路径。要更新Hadoop FS或Local FS目标已完成流传输的整个文件，请使用以下表达式：`${record:value('/targetFileInfo/path')}` |
   | 移动文件                                                     | 选择以移动文件。                                             |
   | 新地点                                                       | 文件的新位置。                                               |
   | 改名                                                         | 选择重命名文件。                                             |
   | 新名字                                                       | 文件的新名称。                                               |
   | [变更拥有权](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HDFSMetadata.html#concept_zr1_5ck_rx) | 选择以更改文件所有者或组。                                   |
   | 新主人                                                       | 成为文件新所有者的用户名。                                   |
   | 新组                                                         | 该组将成为文件的新组所有者。                                 |
   | [设置权限](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HDFSMetadata.html#concept_zr1_5ck_rx) | 选择以八进制或符号格式设置文件权限。                         |
   | 新权限                                                       | 八进制或符号格式的文件权限。                                 |
   | [设定ACL](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HDFSMetadata.html#concept_zr1_5ck_rx) | 选择以定义访问控制列表（ACL）权限。                          |
   | 新的ACL                                                      | 为所有者，组和其他定义ACL。您可以选择定义其他用户和组权限。有关详细信息，请参阅[定义所有者，组，权限和ACL](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HDFSMetadata.html#concept_zr1_5ck_rx)。 |

6. 要删除文件或目录，请配置以下属性：

   | 任务属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 文件路径 | 该表达式表示要删除的文件或目录的完整路径。执行程序以递归方式删除目录，同时也删除所有子目录。请谨慎使用。有关更多信息，请参阅[删除文件或目录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HDFSMetadata.html#concept_yf2_hc4_x1b)。默认情况下，该属性使用 `${record:value('/filepath')}`。**注意：**在大多数情况下，您将不想使用默认表达式。默认表达式更适合于更改文件元数据。 |