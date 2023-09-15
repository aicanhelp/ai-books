# Amazon S3执行器

每次接收事件时，Amazon S3执行程序都会在Amazon S3中执行任务。

收到事件后，执行者可以执行以下任务之一：

- 为指定的内容创建一个新的Amazon S3对象
- 将5 GB以下的对象复制到同一存储桶中的另一个位置，并可以选择删除原始对象
- 将标签添加到现有对象

每个Amazon S3执行程序都可以执行一种类型的任务。要执行其他任务，请使用其他执行程序。

将Amazon S3执行程序用作事件流的一部分。您可以以任何逻辑方式使用执行程序，例如将信息从事件记录写入新的S3对象，或者在对象被Amazon S3目标写入后复制或标记对象。

在配置Amazon S3执行程序时，您可以指定连接信息，例如访问密钥，区域和存储桶。您配置表示对象名称和位置的表达式。创建新对象时，可以指定要放置在对象中的内容。复制对象时，可以指定对象的位置以及副本的位置。您还可以配置执行程序以在复制原始对象后将其删除。将标签添加到现有对象时，可以指定要使用的标签。

您可以选择使用HTTP代理连接到Amazon S3。

您还可以配置执行程序以为另一个事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

## AWS凭证

当Data Collector使用Amazon S3执行器时，它必须将凭证传递给Amazon Web Services。

使用以下方法之一来传递AWS凭证：

- IAM角色

  当执行数据收集器 在Amazon EC2实例上运行时，您可以使用AWS管理控制台为EC2实例配置IAM角色。Data Collector使用IAM实例配置文件凭证自动连接到AWS。

  要使用IAM角色，请不要在目标中配置访问密钥ID和秘密访问密钥属性。

  有关将IAM角色分配给EC2实例的更多信息，请参阅Amazon EC2文档。

- AWS访问密钥对

  当执行数据收集器未在Amazon EC2实例上运行或EC2实例不具有IAM角色时，您必须 在目标中指定**访问密钥ID**和**秘密访问密钥**属性。

  **提示：**为了保护敏感信息（例如访问密钥对）的安全，可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。

**提示：**为了保护敏感信息（例如访问密钥对）的安全，可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。

## 创建新对象

您可以使用Amazon S3执行程序创建新的Amazon S3对象，并在执行程序收到事件记录时将指定的内容写入该对象。

创建对象时，您可以指定创建对象的位置以及要写入该对象的内容。您可以使用表达式来表示对象的位置和要使用的内容。

例如，假设您希望执行者为Amazon S3目标写入的每个对象创建一个新的Amazon S3对象，并使用该新对象存储每个写入对象的记录计数信息。由于对象编写的事件记录包括记录计数，因此您可以启用目标以生成记录并将事件路由到Amazon S3执行程序。

对象写入事件记录包括写入对象的存储桶和对象键。因此，要在与写入对象相同的存储桶中创建新的记录计数对象，可以对Object属性使用以下表达式，如下所示：

```
${record:value('/bucket')}/${record:value('/objectKey')}.recordcount
```

事件记录还包括写入对象的记录数。因此，要将此信息写入新对象，可以对Content属性使用以下表达式，如下所示：

```
${record:value('/recordCount')}
```

**提示：** 阶段生成的事件记录因阶段而异。有关阶段事件的描述，请参见事件发生阶段的文档中的“事件记录”。有关管道事件的描述，请参见[管道事件记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/EventGeneration.html#concept_cv3_nqt_51b)。

## 复制物件

当执行器收到事件记录时，您可以使用Amazon S3执行器将对象复制到同一存储桶中的另一个位置。您可以选择在复制后删除原始对象。该对象的大小必须小于5 GB。

复制对象时，可以指定要复制的对象的位置以及复制的位置。目标位置必须与原始对象在同一存储桶中。您可以使用表达式来表示两个位置。您还可以指定是否删除原始对象。

一个简单的示例是在关闭每个写入的对象后将其移动到Completed目录。为此，您将Amazon S3目标配置为生成事件。由于对象编写的事件记录包含存储桶和对象键，因此可以使用该信息来配置Object属性，如下所示：

```
${record:value('/bucket')}/${record:value('/objectKey')}
```

然后，要将对象移动到Completed目录，并保留相同的对象名称，可以配置“新建对象路径”属性，如下所示：

```
${record:value('/bucket')}/completed/${record:value('/objectKey')}
```

然后，您可以选择“删除原始对象”以删除原始对象。

要执行更复杂的操作，例如仅将带有_west后缀的对象子集移动到其他位置，您可以在事件流中添加流选择器处理器以仅将/ objectKey字段包含_west后缀的事件路由到Amazon S3执行者。

## 标记现有对象

您可以使用Amazon S3执行程序将标签添加到现有的Amazon S3对象。标记是键-值对，可用于对对象进行分类，例如product：<product>。

您可以配置多个标签。配置标签时，可以仅使用键定义标签，也可以指定键和值。您也可以使用表达式来定义标签值。

例如，您可以使用表达式根据事件记录中的recordCount字段指定写入对象的记录数，如下所示：

```
key: processed records
value: ${record:value('/recordCount')}
```

有关标签（包括Amazon S3限制）的更多信息，请参阅[Amazon S3文档](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html)。

## 事件产生



Amazon S3执行程序可以生成您可以在事件流中使用的事件。启用事件生成后，执行程序每次创建新对象，将标签添加到现有对象或完成将对象复制到新位置时都会生成事件。

Amazon S3事件可以任何逻辑方式使用。例如：

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

Amazon S3执行程序生成的事件记录具有以下与事件相关的记录头属性。记录标题属性存储为字符串值。

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下事件类型：文件更改-执行程序将标签添加到现有对象时生成。文件创建-在执行程序创建新对象时生成。文件移动-当执行程序完成将对象复制到新位置时生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

Amazon S3执行程序可以生成以下类型的事件记录：

- 文件更改

  执行程序在将标签添加到现有对象时会生成文件更改的事件记录。文件更改的事件记录的`sdc.event.type` 记录头属性设置为，`file-changed`并包含以下字段：活动栏位名称描述object_key标记对象的键。

- 文件创建

  执行程序在创建新对象时会生成文件创建的事件记录。文件创建的事件记录的`sdc.event.type` 记录头属性设置为，`file-created`并包含以下字段：活动栏位名称描述object_key创建对象的键。

- 文件移动

  执行程序完成将对象复制到新位置后，将生成文件移动的事件记录。文件移动的事件记录的`sdc.event.type`记录头属性设置为，`file-moved`并包含以下字段：活动栏位名称描述object_key复制对象的键。

## 配置Amazon S3执行器

配置Amazon S3执行程序以创建新的Amazon S3对象或向现有对象添加标签。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/AmazonS3.html#concept_jv4_12x_gjb) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击**添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **Amazon S3”**选项卡上，配置以下属性：

   | Amazon S3属性                                                | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 访问密钥ID [![img](imgs/icon_moreInfo-20200310203024449.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/AmazonS3.html#concept_bmp_zlg_vw) | AWS访问密钥ID。不将IAM角色与IAM实例配置文件凭据一起使用时是必需的。 |
   | 秘密访问密钥[![img](imgs/icon_moreInfo-20200310203024449.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/AmazonS3.html#concept_bmp_zlg_vw) | AWS秘密访问密钥。不将IAM角色与IAM实例配置文件凭据一起使用时是必需的。 |
   | 区域                                                         | Amazon S3地区。                                              |
   | 终点                                                         | 当您为区域选择“其他”时要连接的端点。输入端点名称。           |
   | 桶                                                           | 包含要创建，复制或更新的对象的存储桶。**注意：**存储桶名称必须符合DNS。有关存储桶命名约定的更多信息，请参阅[Amazon S3文档](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html)。 |

3. 在“ **任务”**选项卡上，配置以下属性：

   | 任务属性                                                     |                             描述                             |
   | :----------------------------------------------------------- | :----------------------------------------------------------: |
   | 任务                                                         | 收到事件记录后要执行的任务。选择以下选项之一：创建新对象-用于使用配置的内容创建新的S3对象。复制对象-用于将关闭的S3对象复制到同一存储桶中的另一个位置。向现有对象添加标签-用于向封闭的S3对象添加标签。 |
   | 宾语                                                         | 要使用的对象的路径。要使用其闭包生成事件记录的对象，请使用以下表达式：`${record:value('/bucket')}/${record:value('/objectKey)}`要使用其关闭生成事件记录的整个文件，请使用以下表达式：`${record:value('/targetFileInfo/bucket')}/${record:value('/targetFileInfo/objectKey)}` |
   | 内容                                                         | 写入新对象的内容。您可以使用表达式来表示要使用的内容。有关更多信息，请参见[创建新对象](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/AmazonS3.html#concept_mtm_dqx_m1b)。 |
   | 新对象路径                                                   | 复制对象的路径。您可以使用表达式来表示对象的位置和名称。有关更多信息，请参见[复制对象](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/AmazonS3.html#concept_v5j_pjr_f2b)。 |
   | [![img](imgs/icon_moreInfo-20200310203024449.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/AmazonS3.html#concept_gjz_m42_31b) | 要添加到现有对象的标签。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以配置标签。您可以配置多个标签。配置标签时，可以仅使用键定义标签，也可以指定键和值。您也可以使用表达式来定义标签值。 |

4. 要使用HTTP代理，请在“ **高级”**选项卡上，配置以下属性：

   | 先进物业       | 描述                                                         |
   | :------------- | :----------------------------------------------------------- |
   | 使用代理服务器 | 指定是否使用代理进行连接。                                   |
   | 代理主机       | 代理主机。                                                   |
   | 代理端口       | 代理端口。                                                   |
   | 代理用户       | 代理凭据的用户名。                                           |
   | 代理密码       | 代理凭证的密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |