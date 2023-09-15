# ADLS Gen1文件元数据

每次接收事件时，ADLS Gen1文件元数据执行程序都会更改文件元数据，创建一个空文件或删除Azure Data Lake Storage Gen1中的文件或目录。若要在Azure Data Lake Storage Gen2中执行这些任务，请使用[ADLS Gen2文件元数据执行程序](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/ADLS-G2-FileMeta.html#concept_i22_k2k_xhb)。

在使用执行程序之前，必须执行一些必备任务。

您不能在同一执行程序中执行多个任务。要执行多个任务，请使用其他执行程序。

将ADLS Gen1文件元数据执行程序用作事件流的一部分。例如，在执行程序从Azure Data Lake Storage Gen1目标接收到文件关闭事件后，可以使用执行程序来移动文件或更改文件权限。

更改元数据时，可以配置一个表示要处理的文件的位置和名称的表达式，然后指定要执行的更改。创建空文件时，可以指定文件的输出位置，还可以选择指定文件的所有者，权限和ACL。删除文件或目录时，可以指定文件或目录的位置。

必要时，您可以配置高级属性以传递到基础Hadoop文件系统。

您还可以配置执行程序以为另一个事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

有关使用类似文件元数据执行程序的[案例研究](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_d1q_xl4_lx)，请参见[案例研究：输出文件管理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_d1q_xl4_lx)。

## 先决条件



在配置ADLS Gen1文件元数据执行程序之前，请完成以下先决条件：

1. 如有必要，为

   Data Collector

   创建一个新的Azure Active Directory应用程序。

   有关创建新应用程序的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal)。

2. 确保Azure Active Directory 数据收集器 应用程序具有适当的访问控制以执行必要的任务。

   在数据采集 应用程序需要的读，写和执行权限执行所有可能的任务。

   有关配置Gen1访问控制的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-security-overview)

3. [从Azure检索信息](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/ADLS-G1-FileMeta.html#concept_sdw_kfk_xhb)以配置执行程序。

完成所有必备任务后，即可配置执行程序。

### 检索身份验证信息

ADLS Gen1文件元数据执行程序使用Azure Active Directory服务主体身份验证（也称为服务到服务身份验证）连接到Azure。

执行者需要以下Azure身份验证信息：

- 应用程序ID- 

  Azure Active Directory 数据收集器应用程序的应用程序ID 。也称为客户端ID。

  有关从Azure门户访问应用程序ID的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)。

- 身份验证令牌端点- 用于Data Collector的Azure Active Directory v1.0应用程序的OAuth 2.0令牌端点。例如： `https://login.microsoftonline.com//oauth2/token.`

- 应用程序密钥

  -Azure Active Directory 数据收集器应用程序的身份验证密钥 。也称为客户端密钥。

  有关从Azure门户访问应用程序密钥的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)。

## 相关事件产生阶段



在管道的事件流中使用ADLS Gen1文件元数据执行程序。ADLS Gen1文件元数据执行程序经过优化，可以更新由Azure Data Lake Storage Gen1目标或其他ADLS Gen1文件元数据执行程序处理的输出文件或整个文件的文件元数据。但是，您可以以任何逻辑方式使用执行程序。

## 更改元数据



您可以配置ADLS Gen1文件元数据执行程序，以在收到事件后更改Azure Data Lake Storage Gen1中文件的元数据。例如，您可以使用执行程序在目标关闭文件后更改文件许可权。

更改文件元数据时，ADLS Gen1文件元数据执行程序可以同时更改以下文件元数据：

- 文件名
- 档案位置
- 文件所有者和组
- 档案权限
- 访问控制列表（ACL）

若要更改元数据，Data Collector的Azure Active Directory应用程序 必须具有所需的权限。

### 指定文件路径



当使用ADLS Gen1文件元数据执行程序更改文件元数据时，请为“文件路径”属性指定一个表达式，该表达式提供了要使用的文件的绝对路径。

使用默认文件路径表达式`${record:value('/filepath')}`来更新由Azure Data Lake Storage Gen1目标关闭的输出文件。此目标生成的文件关闭事件记录包括一个`filepath` 字段，其中包含关闭的输出文件的位置和名称。

若要更新Azure Data Lake Storage Gen1目标已完成流传输的整个文件，请使用以下表达式：

```
${record:value('/targetFileInfo/path')}
```

来自目标的整个文件处理的事件记录包括一个 `/targetFileInfo/path`字段，其中包含处理的整个文件的位置和名称。

有关由Azure Data Lake Storage Gen1目标生成的事件记录的详细信息，请参阅[事件记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/ADLS-G1-D.html#concept_concept_aps_qhx_zhb)。

### 更改文件名或位置



当使用ADLS Gen1文件元数据执行程序更改文件元数据时，可以在关闭文件后更改文件的名称或位置。指定新的文件名和位置时，可以输入常量或表达式。使用任何求值为您要使用的值的表达式。

需要时，可以在表达式中包括文件函数以指定现有文件路径的一部分。文件功能可以返回路径，文件名或扩展名的任何部分。

- 移动文件示例

  假设Azure Data Lake Storage Gen1目标将JSON文件写入以下目录结构：`/server1/weblogs//`

  写入文件后，您希望ADLS Gen1文件元数据执行程序将文件移动到其他根目录。移动文件时，您需要指定文件的新位置。因此，您可以将执行程序配置为在仍使用路径其余部分的情况下，将文件移动到其他目录，如下所示：`/newDir/${file:pathElement(record:value('/filepath'),1)}/${file:pathElement(record:value('/filepath'),2)}/`

  此表达式使用newDir作为新的根目录，然后使用两个级别的子目录。移动文件时不要包括文件名。

- 重命名文件的示例

  假设您要将.json后缀添加到原始文件名。重命名文件时，需要为文件指定新名称，因此请使用以下表达式：`${file:fileName(record:value('/filepath'))}.json`

  该表达式从`filepath` 事件记录中的字段返回文件名，并添加`.json`到文件名中，例如` .json`。

  如果要从写入文件中删除扩展名，则可以在“新建名称”属性中使用以下表达式：`${file:removeExtension(file:fileName(record:value('/filepath')))}`

  此表达式从`filepath`字段的事件记录中返回文件名，从名称中 删除扩展名，并将结果用作新文件名。

有关文件功能的更多信息，请参见[文件功能](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/Functions.html#concept_kxj_nyl_5x)。

### 定义所有者，组，权限和ACL

使用ADLS Gen1文件元数据执行程序更改文件元数据或创建空文件时，可以定义文件所有者，组，文件权限和访问控制列表（ACL）。

**重要说明：**执行程序更改权限时，将删除现有权限并实现请求的权限。执行程序不会将权限添加到现有权限中，因此请确保完全按照所需的方式配置权限。

您可以使用以下方法的任意组合设置权限：

- 定义新的所有者和组

  您可以定义文件的所有者和组。使用此选项时，必须同时输入所有者和组名。

- 使用八进制或符号格式设置文件权限

  您可以通过输入要使用八进制或符号格式的权限来设置文件权限。

  例如，您可以使用以下八进制格式将文件设置为只读：`0444`您也可以使用以下符号格式将文件设置为只读：`-r--r--r-- `

  要使它们对于用户和组为只读状态，禁止所有其他用户访问，则可以使用以下两种格式之一：`0440 -r--r-----`

- 定义ACL

  您可以定义文件的ACL。定义ACL时，请注意，Azure Data Lake Storage需要为用户，组和其他定义权限。您也可以为其他用户或组添加权限。

  使用以下格式定义ACL：`user::,group::,other::\ [,:]`

  使用符号格式定义权限，其中r，w，x或-表示权限类型。

  例如，以下ACL仅允许用户和组的读取访问权限：`user::r--,group::r--,other::–-`

  如果要允许除与文件关联的组之外的其他访问权限，请输入以下权限：`user::r--,group::r--,other::–-,group:operations:r--`

## 创建一个空文件

可以将ADLS Gen1文件元数据执行程序配置为在收到事件后在Azure Data Lake Storage Gen1中创建空文件。您可能会创建空文件来触发其他应用程序（例如Oozie）中的下游操作。

若要创建一个空文件，请为“文件路径”属性指定一个表达式，该表达式提供指向要在其中创建文件的位置的绝对路径。

**注意：**在大多数情况下，您将不想使用默认表达式。默认表达式更适合于更改文件元数据。

创建空文件时，还可以指定以下文件详细信息：

- 文件所有者和组
- 档案权限
- 访问控制列表（ACL）

有关更多信息，请参见[定义所有者，组，权限和ACL](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/ADLS-G1-FileMeta.html#concept_grb_xfj_zhb)。

## 删除文件或目录

可以将ADLS Gen1文件元数据执行程序配置为在收到事件后从Azure Data Lake Storage Gen1删除文件或目录。

例如，假设您运行一条日常管道，该管道将数据写入Azure Data Lake Storage Gen1。在管道开始处理数据之前，可以使用ADLS Gen1文件元数据执行程序删除目标目录及其所有内容。只需配置管道，即可将管道启动事件传递给ADLS Gen1文件元数据执行程序，然后在配置执行程序时指定目标目录。有关使用管道事件的更多信息，请参见[管道事件生成](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_amg_2qr_t1b)。

小心删除目录。执行程序以递归方式删除目录，除指定目录外，还删除所有子目录及其内容。

## 事件产生

ADLS Gen1文件元数据执行程序可以生成可在事件流中使用的事件。启用事件生成后，执行程序每次更改文件元数据，创建空文件或删除文件或目录时都会生成事件。

ADLS Gen1文件元数据事件可以任何逻辑方式使用。例如：

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

由ADLS Gen1文件元数据执行程序生成的事件记录具有以下与事件相关的记录头属性。记录标题属性存储为字符串值。

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下事件类型：文件更改-执行程序更改文件元数据（包括文件名，位置，权限或ACL）时生成。file-created-执行程序创建一个空文件时生成。文件删除-在执行程序删除文件或目录时生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

ADLS Gen1文件元数据执行程序可以生成以下类型的事件记录：

- 文件已更改

  执行程序在更改文件元数据（包括文件名，位置，权限或ACL）时会生成文件更改的事件记录。文件更改的事件记录的`sdc.event.type` 记录头属性设置为，`file-changed`并包括以下字段：活动栏位名称描述文件路径更改文件的最新路径和名称。文件名更改文件的最新名称。

- 文件已创建

  执行程序在创建一个空文件时会生成一个文件创建的事件记录。文件创建的事件记录的`sdc.event.type` 记录头属性设置为，`file-created`并包括以下字段：活动栏位名称描述文件路径文件创建的位置。文件名文件名。

- 档案已移除

  执行程序在删除文件或目录时会生成一个文件删除的事件记录。除去文件的事件记录的`sdc.event.type` 记录头属性设置为`file-removed `，包括以下字段：活动栏位名称描述文件路径被删除目录的位置，或被删除文件所在的目录。文件名删除的文件名（如果适用）。

## 配置ADLS Gen1文件元数据执行器



配置ADLS Gen1文件元数据执行程序以在收到事件后创建一个空文件，更改文件元数据，或从Azure Data Lake Storage Gen1删除文件或目录。

在使用执行程序之前，必须执行一些[必备任务](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/ADLS-G1-FileMeta.html#concept_lfd_2fk_xhb)。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/ADLS-G1-FileMeta.html#concept_xjb_tjj_zhb) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击**添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **数据湖”**选项卡上，配置以下属性：

   | 数据湖物业   | 描述                                                         |
   | :----------- | :----------------------------------------------------------- |
   | 申请编号     | Azure Active Directory 数据收集器应用程序的应用程序ID 。也称为客户端ID。有关从Azure门户访问应用程序ID的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)。 |
   | 验证令牌端点 | 用于Data Collector的Azure Active Directory v1.0应用程序的OAuth 2.0令牌终结点。例如： `https://login.microsoftonline.com//oauth2/token.` |
   | 帐户FQDN     | Azure Data Lake Storage Gen1帐户的主机名。例如：`.azuredatalakestore.net` |
   | 应用密钥     | Azure Active Directory 数据收集器应用程序的身份验证密钥 。也称为客户端密钥。有关从Azure门户访问应用程序密钥的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)。 |
   | 进阶设定     | 要传递给基础文件系统的其他HDFS属性。ADLS Gen1使用Hadoop FileSystem接口访问数据。指定的属性将覆盖Hadoop配置文件中的属性。要添加属性，请单击“ **添加”**图标并定义HDFS属性名称和值。使用Hadoop期望的属性名称和值。 |

3. 在“ **任务”**选项卡上，配置以下属性：

   | 任务属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 任务     | 执行者执行的任务类型。您可以创建一个空文件，更改文件元数据或删除文件或目录。要执行多种类型的任务，请将其他执行程序添加到管道中。 |

4. 要创建一个空文件，请配置以下属性：

   | 任务属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 文件路径                                                     | 表示要创建的文件的完整路径的表达式。默认情况下，该属性使用 `${record:value('/filepath')}`。**注意：**在大多数情况下，您将不想使用默认表达式。默认表达式更适合于更改文件元数据。 |
   | [集合所有权](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/ADLS-G1-FileMeta.html#concept_grb_xfj_zhb) | 选择以指定文件所有者或组。                                   |
   | 新主人                                                       | 成为文件新所有者的用户名。                                   |
   | 新组                                                         | 该组将成为文件的新组所有者。                                 |
   | [设置权限](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/ADLS-G1-FileMeta.html#concept_grb_xfj_zhb) | 选择以八进制或符号格式设置文件权限。                         |
   | 新权限                                                       | 八进制或符号格式的文件权限。                                 |
   | [设定ACL](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/ADLS-G1-FileMeta.html#concept_grb_xfj_zhb) | 选择以定义访问控制列表（ACL）权限。                          |
   | 新的ACL                                                      | 为所有者，组和其他定义ACL。您可以选择定义其他用户和组权限。  |

5. 要更改文件元数据，请配置以下属性：

   | 任务属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [文件路径](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/ADLS-G1-FileMeta.html#concept_mtm_n3v_yhb) | 表示文件完整路径的表达式。默认情况下，该属性使用 `${record:value('/filepath')}`，用于处理`filepath`字段中的数据。Azure Data Lake Storage Gen1目标会生成文件关闭事件记录，其中包括`filepath` 字段中已关闭文件的路径。若要更新Azure Data Lake Storage Gen1目标已完成流传输的整个文件，请使用以下表达式：`${record:value('/targetFileInfo/path')}` |
   | 移动文件                                                     | 选择以移动文件。                                             |
   | 新地点                                                       | 文件的新位置。                                               |
   | 改名                                                         | 选择重命名文件。                                             |
   | 新名字                                                       | 文件的新名称。                                               |
   | [集合所有权](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/ADLS-G1-FileMeta.html#concept_grb_xfj_zhb) | 选择以更改文件所有者或组。                                   |
   | 新主人                                                       | 拥有文件的用户名。                                           |
   | 新组                                                         | 拥有文件的组。                                               |
   | [设置权限](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/ADLS-G1-FileMeta.html#concept_grb_xfj_zhb) | 选择以八进制或符号格式设置文件权限。                         |
   | 新权限                                                       | 八进制或符号格式的文件权限。                                 |
   | [设定ACL](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/ADLS-G1-FileMeta.html#concept_grb_xfj_zhb) | 选择以定义访问控制列表（ACL）权限。                          |
   | 新的ACL                                                      | 为所有者，组和其他定义ACL。您可以选择定义其他用户和组权限。  |

6. 要删除文件或目录，请配置以下属性：

   | 任务属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 文件路径 | 该表达式表示要删除的文件或目录的完整路径。执行程序以递归方式删除目录，同时也删除所有子目录。请谨慎使用。有关更多信息，请参阅[删除文件或目录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/ADLS-G1-FileMeta.html#concept_jtt_s3j_zhb)。默认情况下，该属性使用`${record:value('/filepath')}`。**注意：**在大多数情况下，您将不想使用默认表达式。默认表达式更适合于更改文件元数据。 |