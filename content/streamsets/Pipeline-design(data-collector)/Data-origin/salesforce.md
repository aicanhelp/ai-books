# 销售队伍

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310173743200.png) 资料收集器

Salesforce源从Salesforce读取数据。您可以将源配置为通过以下一种或两种方式读取数据：

- 执行查询以使用Bulk API或SOAP API从Salesforce读取现有数据。

  处理现有数据时，可以配置SOQL查询，偏移量字段和可选的初始偏移量以供使用。使用Bulk API时，可以启用PK Chunking来有效处理大量数据。

  在处理现有数据且未订阅通知时，可以将源配置为重复SOQL查询。原点可以按指定的时间间隔执行完整或增量读取。在某些情况下，您还可以处理已删除的记录。

- 订阅通知以处理PushTopic，平台或更改事件。

  订阅通知以处理事件时，您可以指定事件类型以及主题，API或更改数据捕获对象的名称。订阅平台事件时，您还可以指定一个重播属性。

默认情况下，源将生成提供有关每个记录和字段的其他信息的Salesforce记录标题属性和Salesforce字段属性。原始记录记录头属性中还包括CRUD操作类型，因此生成的记录可以由启用CRUD的目标轻松处理。有关Data Collector更改的数据处理的概述以及启用CRUD的目标的列表，请参阅[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

您可以指定用于Salesforce属性的前缀，也可以完全禁用属性生成。您还可以选择禁用查询验证或使用HTTP代理连接到Salesforce。

在Salesforce中启用后，您可以配置源以使用相互身份验证来连接到Salesforce。

源可以为事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

## 查询现有数据

Salesforce源可以执行查询以从Salesforce读取现有数据。使用Salesforce对象查询语言（SOQL）编写查询。

当您配置来源以查询现有数据时，您可以指定来源是使用Salesforce批量API还是SOAP API从Salesforce读取。批量API经过优化，可处理大量数据。使用Bulk API时，可以为较大的数据集启用PK Chunking。SOAP API比Bulk API支持更复杂的查询。例如，要使用聚合函数，必须使用SOAP API。但是，在处理大量数据时，SOAP API不太实用。有关何时使用Bulk或SOAP API的更多信息，请参阅Salesforce Developer文档。

Salesforce原点使用偏移量字段和初始偏移量或起始ID来确定从何处开始读取对象中的数据。默认情况下，偏移量字段定义为SalesforceID 系统字段，其中包含Salesforce对象中每个记录的唯一标识符。

当您将来源配置为查询现有数据并且不订阅通知时，可以将来源配置为运行一次查询或重复查询。一次运行查询时，管道从Salesforce对象读取完所有数据后就会停止。如果再次启动管道，则原点将使用初始偏移量或起始ID开始读取，从而再次读取整个现有数据集。

如果管道在完成读取所有数据之前停止，则Salesforce原点将保存最近读取的偏移值。当管道再次启动时，原点将使用最后一个读取的偏移值从其停止处继续进行处理。您可以重置原点以处理所有请求的对象。

当您将原点配置为多次运行查询时，管道会连续运行，因此它可以定期重复查询。您可以选择原点重复查询的方式。有关更多信息，请参见[重复查询](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_owv_nj5_2z)。

在极少数情况下，查询返回的数据类型与字段的架构中指定的数据类型不匹配。例如，当架构指定整数时，查询可能返回浮点数。使用高级的“不匹配类型行为”属性可以配置源如何处理此类数据类型不匹配。原点可以保留返回的数据，截断返回的数据以匹配指定的类型，或舍入返回的数据以匹配指定的类型。

## 使用SOAP和Bulk API

您可以使用SOAP或Bulk API查询现有的Salesforce数据。查询现有数据时，您定义SOQL查询和相关属性以确定从Salesforce返回的数据。

在不使用PK分块的情况下使用SOAP API或批量API时，请遵循以下准则：

- SOQL查询

  使用SOAP API或Bulk API处理现有数据时，请使用以下查询准则：在WHERE子句中，包括offset字段和offset值。原点使用偏移量字段和值来确定返回的数据。将两者都包含在查询的WHERE子句中。在WHERE子句中，使用OFFSET常量表示偏移值。使用$ {OFFSET}代表偏移值。例如，当您启动管道时，以下查询将从对象返回所有数据，其中偏移字段中的数据大于初始偏移值：`SELECT Id, Name FROM  WHERE  > ${OFFSET}`**提示：**当偏移量值为字符串时，请将$ {OFFSET}括在单引号中。在ORDER BY子句中，将offset字段作为第一个字段。为避免返回重复的数据，请在ORDER BY子句中将offset字段用作第一个字段。**注意：**使用的字段不是 ID ORDER BY子句中的字段会降低性能。使用SOAP API处理现有数据时，可以在SOQL查询的SELECT语句中包括[SOQL聚合函数](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_oc2_zsj_lhb)。批量API不支持聚合函数。完整的SOQL查询应使用以下语法：`SELECT , , , ... FROM  WHERE  > ${OFFSET} ORDER BY `如果`SELECT * FROM `在SOQL查询中指定，则源将扩展`*`到Salesforce对象中所有可供配置用户访问的字段。请注意，起源将复合字段的组成部分添加到查询中，而不是添加复合字段本身。例如，来源添加BillingStreet，BillingCity等，而不是添加BillingAddress。同样，它添加Location__Latitude__s和Location__Longitude__s而不是Location__c。

  必要时，您可以配置源以跳过验证查询。当您知道查询有效但不符合验证要求时，请跳过查询验证。例如，如果省略ORDER BY子句，则必须禁用查询验证。您可以省略ORDER BY子句以提高大型查询的性能。若要禁用查询验证，请使用“高级”选项卡上的“禁用查询验证”属性。

- 其他特性

  使用SOAP API或Bulk API处理现有数据时，请在“查询”选项卡上配置以下其他属性：偏移字段-通常是 ID系统字段，偏移字段应为记录中的索引字段。默认为ID 领域。初始偏移- 管道启动时或重置原点后要使用的第一个偏移值。包括已删除的记录-可选属性。确定SOQL查询是否还从Salesforce回收站中检索已删除的记录。当阶段使用Salesforce SOAP API或Bulk API版本39.0或更高版本时，查询可以检索已删除的记录。早期版本的Bulk API不支持检索已删除的记录。

### 例

假设您想一次从Salesforce客户对象读取所有名称和帐号。该对象包含大量记录，因此您选择使用Salesforce Bulk API。

要处理数据，请在“查询”选项卡上配置以下属性：

- 使用批量API-启用批量API。

- SOQL查询-在WHERE和ORDER BY子句中包含offset字段和offset值，以及要返回的字段，如下所示：

  ```
  SELECT Id, Name, AccountNumber FROM Account WHERE Id > '${OFFSET}' ORDER BY Id
  ```

- 重复查询-设置为“不重复”一次运行查询。

- 初始偏移量-使用十五个零的默认值（`000000000000000`）作为偏移量值，以确保原点读取对象中的所有记录。

- 偏移字段-将默认值`Id`用作偏移字段。

### SOQL查询中的聚合函数



使用SOAP API查询现有Salesforce数据时，可以在SOQL查询的SELECT语句中包括SOQL聚合函数。原点将查询的第一个函数 `expr0`的结果放入`expr1`字段中，同一查询的第二个函数的结果放入字段中，依此类推。生成的字段类型取决于函数和查询的字段。该阶段不会为聚合函数生成的字段生成字段标题属性。按非聚合字段分组时，只能在同一SELECT语句中同时包含聚合函数和非聚合字段。

以下示例演示了SOQL查询中聚合函数的某些用法。每个示例都从名称以开头的Account对象读取数据 `East`。

#### 按条款分组

您可以将聚合函数与GROUP BY子句结合使用，以计算记录组的值。

假设对于以开头的记录`East`，您需要一个行业列表以及记录计数，最后修改日期以及按`Industry`字段分组的最小雇员数。

您可以输入以下查询：

```
SELECT Industry, COUNT(Id), MAX(LastModifiedDate), MIN(NumberOfEmployees) FROM Account 
WHERE Id > '${OFFSET}' AND Name LIKE 'East%' 
GROUP BY Industry
```

原点将查询结果放入以下字段：

- `Industry`
- `expr0` -整数字段包含记录数
- `expr1` -日期时间字段包含最近修改的日期
- `expr2` -整数字段包含最少数量的员工

#### 现场别名

您可以在查询中使用字段别名来指定将原点放置函数结果的字段名称。

假设在上一个示例中，您想要将记录计数放入 `cnt`字段中，将最后修改日期放入 `max_modify`字段中，并将最小雇员数放入该 `min_employees`字段中。

您可以输入以下查询：

```
SELECT Industry, COUNT(Id) cnt, MAX(LastModifiedDate) max_modify, MIN(NumberOfEmployees) min_employees FROM Account
WHERE Id > '${OFFSET}' AND Name LIKE 'East%' 
GROUP BY Industry
```

原点将查询结果放入以下字段：

- `Industry`
- `cnt`
- `max_modify`
- `min_employees`

您不能将SOQL关键字（例如）指定`count`为别名。

## 将Bulk API与PK Chunking一起使用

您可以将PK Chunking与Bulk API结合使用来处理大量Salesforce数据。PK Chunking使用 ID 字段作为offset字段，并根据用户定义的数据块返回数据块 ID领域。有关PK Chunking的更多信息，请参阅[Salesforce文档](https://developer.salesforce.com/docs/atlas.en-us.api_asynch.meta/api_asynch/async_api_headers_enable_pk_chunking.htm?search_text=chunking)或此信息 [博客文章](https://developer.salesforce.com/blogs/engineering/2015/03/use-pk-chunking-extract-large-data-sets-salesforce.html)。

执行PK分块时，原点无法处理已删除的记录。

将Bulk API与PK Chunking一起使用时，请遵循以下准则：

- SOQL查询

  使用以下查询准则：包括 ID SELECT语句中的字段。（可选）包括WHERE子句，但不要使用 ID WHERE子句中的字段。不要包含ORDER BY子句。

  PK分块的完整SOQL查询应使用以下语法：`SELECT Id, , , ... [WHERE ] FROM `

  如果`SELECT * FROM `在SOQL查询中指定，则源将扩展`*`到Salesforce对象中所有可供配置用户访问的字段。请注意，起源将复合字段的组成部分添加到查询中，而不是添加复合字段本身。例如，来源添加BillingStreet，BillingCity等，而不是添加BillingAddress。同样，它添加Location__Latitude__s和Location__Longitude__s而不是Location__c。

- 其他特性

  在查询选项卡上配置以下其他属性：偏移字段-用于分块的字段。必须使用默认值 ID 领域。块大小- 值范围ID 一次要查询的字段。 默认值为100,000，最大大小为250,000。起始ID- 第一个块的可选下边界。如果省略，则原点将从对象中的第一条记录开始处理。

  例如，当使用250,000的块大小和001300000000000的开始ID时，第一个查询返回ID值从001300000000000开始的数据，块大小为250,000。第二个查询返回下一个记录块。

  使用PK块时，原点将忽略“初始偏移”属性，而是使用可选的“开始ID”。

### 例

假设您要复制Salesforce Order对象中的所有数据。该对象包含大量记录，因此您想将Salesforce Bulk API与PK块一起使用。

要处理数据，请在“查询”选项卡上配置以下属性：

- 使用批量API-启用批量API。

- 使用PK分组-启用PK分组。还必须在您的Salesforce环境中启用PK Chunking。

- 块大小-设置块大小以定义可一次查询的Id字段中的范围值。最多使用250,000条记录来返回尽可能多的记录。

- 起始编号-要处理所有可用数据，请不要为此属性输入值。使用此属性代替“初始偏移”来确定`Id`要处理的值的下边界。

- SOQL查询-要处理Order对象中的所有数据，请使用以下查询：

  ```
  SELECT * FROM Order
  ```

  请注意，PK Chunking查询不包含ORDER BY子句。

- 重复查询-设置为“不重复”一次运行查询。

- 初始偏移量-跳过此属性，因为PK Chunking改用Start Id属性。

- 偏移字段-将默认值`Id`用作偏移字段。

## 重复查询

当Salesforce原始处理现有数据且未订阅通知时，它可以定期重复指定的查询。您可以通过以下方式配置源以重复查询：

- 不再重复

  原点不重复查询。原点运行一次查询，然后在完成对Salesforce对象中所有数据的处理后，管道将停止。

- 重复完整查询

  当原点重复完整查询时，它将在每次请求数据时使用初始偏移量或起始ID作为查询中的偏移量值运行定义的查询。

  重复完整查询以捕获所有记录更新。您可以在管道中使用Record Deduplicator，以最大程度地减少重复记录。对于具有大量记录的对象而言并不理想。

- 重复增量查询

  当原点重复增量查询时，它将使用初始偏移量或起始ID作为第一个查询中的偏移量值。

  当原点完成对第一个查询的结果的处理时，它将保存它处理的最后一个偏移值。重复查询时，它使用最后保存的偏移量执行增量查询。增量查询仅处理在上一次查询之后到达的数据子集。必要时，您可以重置原点以使用初始偏移量或起始ID值。

  对仅追加对象或不需要捕获对较旧记录的更改时，重复增量查询。

## 订阅通知

Salesforce源可以订阅通知以处理以下Salesforce事件类型：

- 来自Streaming API的PushTopic事件，以接收有关Salesforce数据更改的通知
- CometD的平台事件以处理事件驱动的数据
- 将事件从CometD更改为处理更改数据捕获数据

### 处理PushTopic事件

要配置来源以订阅PushTopic事件消息，必须首先在Salesforce中基于SOQL查询创建PushTopic。PushTopic查询定义了创建，更新，删除或取消删除事件的记录会生成通知。如果记录更改符合PushTopic查询的条件，则订阅的客户端将生成并接收通知。

Salesforce的来源是订阅PushTopic的客户端。在原始配置中，指定PushTopic的名称，该名称将原始订阅到PushTopic通道。

当您启动配置为订阅Salesforce通知的管道时，管道将连续运行，并在源中接收记录中任何已更改的数据事件。

**注意：**流API将PushTopic事件存储24小时。如果管道停止，然后在24小时内重新启动，则源可以接收有关过去事件的通知。但是，如果管道超过24小时处于非活动状态，则起点可能会错过一些事件。

有关创建PushTopic查询的更多信息，请参阅Salesforce Streaming API开发人员文档。

#### PushTopic事件记录格式

当PushTopic遇到更改事件并生成通知时，它会将事件作为JSON消息以以下格式发送到订阅Salesforce来源的事件：

```
{
  "channel": "/topic/AccountUpdates",
  "clientId": "j24ylcz8l0t0fyp0pze6uzpqlt",
  "data": {
    "event": {
      "createdDate": "2016-09-15T06:01:40.000+0000",
      "type": "updated"
    },
    "sobject": {
      "AccountNumber": "3221320",
      "Id": "0013700000dC9xLAAS",
      "Name": "StreamSets",
      ...more fields...
    }
  }
}
```

该`data/event/type`属性指示更改的类型-创建，更新，删除或取消删除。

当Salesforce来源接收到数据时，它会创建一条记录，该记录的字段名称和值与`data/sobject`消息的属性相对应。

记录还包括`data/event`与消息属性相对应的记录头属性 ，如[Salesforce头属性中所述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_psx_1wg_cy)。

### 处理平台事件

Salesforce来源使用CometD订阅平台事件。在处理平台事件之前，请设置平台事件通道名称，并在Salesforce环境中定义平台事件。

配置源时，可以指定通道名称和要处理的事件消息集。源可以处理最近24小时内的频道中的事件以及任何新事件。或者，它只能处理在启动管道之后到达的新事件。

有关平台事件的更多信息，请参阅[Salesforce文档](https://developer.salesforce.com/docs/atlas.en-us.platform_events.meta/platform_events/platform_events_intro.htm)。

### 处理变更事件

Salesforce来源使用CometD订阅对象的更改事件。为了使源能够处理变更事件，您必须配置Salesforce环境以为特定对象启用Salesforce变更数据捕获。

您可以将原点配置为处理单个对象，也可以将原点配置为处理为更改数据捕获启用的所有对象。Salesforce商店会更改事件三天。如果管道停止，然后在三天内重新启动，则源将收到有关过去事件的通知。但是，如果管道超过三天处于非活动状态，则源可能会错过一些事件。

在处理变更事件时，源会从Salesforce变更事件标头创建记录标头属性。对于Salesforce更改事件标头中的每个字段，源通过将`salesforce.cdc.`前缀添加到字段来创建记录标头属性 。例如，原点创建`salesforce.cdc.entityName`记录头属性，并将其值设置`entityName`为更改事件头中字段的值。

更改事件可以应用于多个Salesforce记录。该`recordIds` change事件报头字段中列出了适用的记录ID。源创建 `salesforce.cdc.recordIds`记录标题属性，该属性包含以逗号分隔的受影响Salesforce记录列表。

原始设置适当的其他记录头属性。源根据更改事件标头中的字段将 `sdc.operation.type`记录标头属性设置为[CRUD操作值](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_yns_y2m_5y)`changeType`。源将`salesforce.sobjectType`记录标头属性设置为`entityName`更改事件标头中字段的值。

**注意：**源将`entityName`更改事件标头中的字段值写入两个记录标头属性。

有关更改事件的更多信息，请参阅[Salesforce文档](https://developer.salesforce.com/docs/atlas.en-us.change_data_capture.meta/change_data_capture/cdc_intro.htm)。

## 读取自定义对象或字段

如果源读取自定义Salesforce对象或字段，则可能要在管道中使用“字段重命名器”来重命名自定义字段。

如果扩展Salesforce对象，则自定义对象和字段名称将附加后缀 `__c`。例如，如果您创建一个自定义Transaction对象，则Salesforce将该对象命名`Transaction__c`。交易对象可能包含名为的字段`Credit_Card__c, Fare_Amount__c, and Payment_Type__c`。

`__c`您可以添加字段重命名器以从字段名称中删除后缀，而不是在管道的其余部分中使用在字段后缀后加上字段名称。

有关Salesforce自定义对象的更多信息，请参阅Salesforce文档。

## 处理已删除的记录

Salesforce源可以从Salesforce回收站中检索已删除的记录以进行处理。

源可以在以下任一情况下处理已删除的记录：

- 使用SOAP API版本39.0或更高版本。
- 不使用PK分块时，请使用Bulk API版本39.0或更高版本。

若要处理已删除的记录，请使用“查询”选项卡上的“包括已删除的记录”属性。

## Salesforce属性

Salesforce源生成Salesforce记录标题属性和Salesforce字段属性，这些属性提供有关每个记录和字段的其他信息。来源从Salesforce接收这些详细信息。

Salesforce属性包括用户定义的前缀，以将Salesforce属性与其他属性区分开。默认情况下，前缀为 `salesforce.`。您可以更改原点使用的前缀，并且可以配置原点以不创建Salesforce属性。

### Salesforce标头属性

Salesforce原始生成Salesforce记录标题属性，该属性提供有关每个记录的其他信息，例如记录的源对象。来源从Salesforce接收这些详细信息。

您可以使用 `record:attribute`或`record:attributeOrDefault` 函数来访问属性中的信息。

Salesforce源可以提供以下Salesforce标头属性：

| Salesforce标头属性               | 描述                                                         |
| :------------------------------- | :----------------------------------------------------------- |
| <Salesforce前缀> sobjectType     | 提供用于记录的Salesforce源对象。当原点执行查询或订阅通知时生成。 |
| <Salesforce前缀> cdc.createdDate | 提供Salesforce PushTopic遇到更改事件的日期。当原点订阅通知时生成。 |
| <Salesforce前缀> cdc.type        | 提供Salesforce PushTopic遇到的更改类型-创建，更新，删除或取消删除。当原点订阅通知时生成。 |
| salesforce.cdc。<更改事件字段>   | 提供更改事件标头中字段的值。当源订阅“更改数据捕获”通知时生成。 |

有关记录标题属性的更多信息，请参见[记录标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)。

### CRUD操作标题属性

当Salesforce原点订阅通知并从PushTopic读取更改的数据时，原点在sdc.operation.type标头属性中包括记录的CRUD操作类型。

如果您在诸如JDBC Producer或Elasticsearch之类的管道中使用启用CRUD的目标，则该目标可以在写入目标系统时使用操作类型。必要时，可以使用表达式评估器或脚本处理器来处理`sdc.operation.type`header属性中的值 。有关Data Collector更改的数据处理的概述以及启用CRUD的目标的列表，请参阅[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

Salesforce原始使用sdc.operation.type记录标题属性中的以下值表示操作类型：

- INSERT为1
- 2个代表删除
- 3更新
- 5用于不受支持的操作
- 未删除的6

**提示：**未删除的记录仅包含记录ID。如果需要记录数据，则可以使用Salesforce查找来检索它。

### Salesforce字段属性

Salesforce原点生成Salesforce字段属性，该属性提供有关每个字段的其他信息，例如Salesforce字段的数据类型。来源从Salesforce接收这些详细信息。

您可以使用 `record:fieldAttribute`或 `record:fieldAttributeOrDefault`函数来访问属性中的信息。

Salesforce源可以提供以下Salesforce字段属性：

| Salesforce字段属性              | 描述                                     |
| :------------------------------ | :--------------------------------------- |
| <Salesforce前缀> salesforceType | 提供该字段的原始Salesforce数据类型。     |
| <Salesforce前缀>长度            | 提供所有字符串和textarea字段的原始长度。 |
| <Salesforce前缀>精度            | 为所有双字段提供原始精度。               |
| <Salesforce前缀>规模            | 提供所有双字段的原始比例。               |
| <Salesforce前缀>数字            | 提供所有整数字段的最大位数。             |

有关字段属性的更多信息，请参见[字段属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)。

## 事件产生

Salesforce源可以生成可在事件流中使用的事件。启用事件生成后，原始将在完成对指定查询返回的数据的处理后生成一个事件。

Salesforce事件可以任何逻辑方式使用。例如：

- 当原始完成处理可用数据时，使用Pipeline Finisher执行程序停止管道并将管道转换为Finished状态。

  重新启动由Pipeline Finisher执行程序停止的管道时，原始服务器将根据您配置原始服务器的方式来处理数据。例如，如果将原点配置为重复增量查询，则当执行程序停止管道时，原点将保存偏移量。重新启动时，原点将从上次保存的偏移开始继续处理。如果将原点配置为重复完整查询，则在重新启动管道时，原点将使用初始偏移量。

  有关示例，请参见[案例研究：停止管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_kff_ykv_lz)。

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储有关已完成查询的信息的目标。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

由Salesforce来源生成的事件记录具有以下与事件相关的记录标题属性：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下类型：no-more-data-当原点完成对查询返回的所有数据的处理时生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

无数据事件记录不包含任何记录字段。

## 更改API版本

Data Collector随附43.0版的Salesforce Web服务连接器库。如果需要访问版本43.0中不存在的功能，则可以使用其他Salesforce API版本 。

1. 在**Salesforce**选项卡上，将**API Version**属性设置为要使用的版本，例如39.0。

2. 从Salesforce Web服务连接器（WSC）下载以下JAR文件的相关版本：

   - WSC JAR文件-force-wsc- <version> .0.0.jar
   - 合作伙伴API JAR文件-force-partner-api- <版本> .0.0.jar

   其中<version>是API版本号，例如39。

   有关从Salesforce WSC下载库的信息，请参阅https://developer.salesforce.com/page/Introduction_to_the_Force.com_Web_Services_Connector。

3. 在以下Data Collector目录中，将默认的force-wsc-43.0.0.jar和force-partner-api-43.0.0.jar文件替换为您下载的版本化JAR文件：

   ```
   $SDC_DIST/streamsets-libs/streamsets-datacollector-salesforce-lib/lib/
   ```

4. 重新启动Data Collector， 以使更改生效。

## 配置Salesforce来源

配置Salesforce原点以从Salesforce读取数据。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_cvb_bvr_kz) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **Salesforce”**选项卡上，配置以下属性：

   | Salesforce财产                                               | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 用户名                                                       | Salesforce用户名，采用以下电子邮件格式： `@.com`。           |
   | 密码                                                         | Salesforce密码。如果运行Data Collector的计算机不在Salesforce环境中配置的受信任IP范围内，则必须生成安全令牌，然后将此属性设置为密码，后跟安全令牌。例如，如果密码为`abcd`，安全令牌为`1234`，则将此属性设置为abcd1234。有关生成安全令牌的更多信息，请参阅[重置安全令牌](https://help.salesforce.com/articleView?id=user_security_token.htm&type=0)。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 验证端点                                                     | Salesforce SOAP API身份验证端点。输入以下值之一：`login.salesforce.com` -用于连接到Production或Developer Edition组织。`test.salesforce.com` -用于连接到沙盒组织。默认值为`login.salesforce.com`。 |
   | [API版本](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#task_ssp_krd_dy) | 用于连接到Salesforce的Salesforce API版本。默认值为43.0。如果更改版本，则还必须从Salesforce Web服务连接器（WSC）下载相关的JAR文件。 |
   | 查询现有数据                                                 | 确定是否执行查询以从Salesforce读取现有数据。                 |
   | 订阅通知                                                     | 确定是否订阅通知以处理事件消息。                             |
   | 最大批次大小（记录）                                         | 一次处理的最大记录数。接受的值最高为Data Collector的最大批处理大小。默认值是1000 数据采集器默认设置为1000。 |
   | [批处理等待时间（毫秒）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ypd_vgr_5q) | 发送部分或空批次之前要等待的毫秒数。                         |

3. 要查询现有数据，请在“ **查询”**选项卡上配置以下属性：

   | [查询属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_djn_x4c_tx) | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 使用批量API                                                  | 确定阶段是使用Salesforce批量API还是SOAP API写入Salesforce。选择以使用批量API。清除以使用SOAP API。 |
   | [使用PK分块](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_aq1_kd1_5cb) | 允许使用PK块处理大量数据。需要配置SOQL查询和其他属性。仅适用于批量API。 |
   | 块大小                                                       | 值的范围 ID 一次要查询的字段。默认值为100,000，最大大小为250,000。仅适用于PK Chunking。 |
   | 起始编号                                                     | 第一块的可选下边界。如果省略，则原点将从对象中的第一条记录开始处理。仅适用于PK Chunking。 |
   | SOQL查询                                                     | 从Salesforce读取现有数据时要使用的SOQL查询。根据您使用的是[SOAP还是不使用PK Chunking](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_vdt_dxc_tx)的[Bulk API](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_aq1_kd1_5cb)或使用了[PK Chunking](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_vdt_dxc_tx)的[Bulk API，](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_aq1_kd1_5cb) SOQL查询要求有所不同。 |
   | [包括已删除的记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_r24_j11_5cb) | 确定SOQL查询是否还从Salesforce回收站中检索已删除的记录。当阶段使用Salesforce SOAP API或Bulk API版本39.0或更高版本时，查询可以检索已删除的记录。启用PK Chunking时，此属性不能与Bulk API一起使用。 |
   | [重复查询](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_owv_nj5_2z) | 确定原点是否多次运行查询。当源处理现有数据且未订阅通知时可用。选择以下选项之一：不重复-不重复查询。运行一次查询，然后在处理完所有数据后管道停止。重复完整查询-在每个查询中使用初始偏移量或起始ID重复查询。重复增量查询-对第一个查询使用初始偏移量或起始ID，然后对后续查询使用最后保存的偏移量重复查询。 |
   | 查询间隔                                                     | 在两次查询之间等待的时间。输入基于时间单位的表达式。您可以使用SECONDS，MINUTES或HOURS。默认值为1分钟：$ {1 * MINUTES}。 |
   | 初始偏移                                                     | 管道启动时或重置原点后要使用的第一个偏移值。默认值为15个零： `000000000000000`。当原点执行PK分块时不使用。 |
   | 偏移场                                                       | 通常情况下 ID 系统字段，偏移字段应为记录中的索引字段。默认为 ID 领域。启用PK分块时使用默认设置。 |

4. 对于订阅了通知的源，在“ **订阅”**选项卡上，配置以下属性：

   | 订阅物业                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [订阅类型](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_nnd_y4c_tx) | 选择要处理的通知类型：[推送主题](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_xws_n3g_5cb) -用于处理PushTopic事件。[平台事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_cwb_mkg_5cb) -用于处理平台事件。[变更数据捕获](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_cwb_mkg_5cc) -用于处理变更事件。 |
   | 推送主题                                                     | 要使用的PushTopic的名称。必须在您的Salesforce环境中定义PushTopic。仅适用于PushTopic事件。 |
   | 平台事件API名称                                              | 要使用的平台事件通道或主题的名称，例如Notification__e。必须在您的Salesforce环境中定义平台事件。仅适用于平台事件。 |
   | 重播选项                                                     | 确定要处理的平台事件：新事件-仅管道启动后广播的事件。所有事件-最近24小时内广播的所有事件以及管道启动后广播的所有新事件。仅适用于平台事件。 |
   | 更改数据捕获对象                                             | 原始处理更改事件的Salesforce对象的API名称。在Salesforce中，必须为对象启用“更改数据捕获”。有关更多信息，请参阅[Salesforce文档](https://developer.salesforce.com/docs/atlas.en-us.change_data_capture.meta/change_data_capture/cdc_select_objects.htm)。例如，您可以输入 Contact或 MyObject__c。留空以处理所有启用了更改数据捕获的对象的更改事件。仅适用于变更事件。 |
   | 流缓冲区大小                                                 | 源可以在流缓冲区中存储的最大字节数。如果管道产生缓冲容量错误，请增加缓冲区大小。 |

5. 在“ **高级”**选项卡上，配置以下属性：

   | 先进物业                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [创建Salesforce属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_qgj_hd2_kz) | 将Salesforce标头属性添加到记录，将字段属性添加到字段。默认情况下，原点创建Salesforce属性。 |
   | Salesforce属性前缀                                           | Salesforce属性的前缀。                                       |
   | 禁用查询验证                                                 | 禁用SOQL查询的查询验证。根据您使用的是[不带PK Chunking](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_vdt_dxc_tx)的[SOAP还是Bulk API或带PK Chunking](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_vdt_dxc_tx)的[Bulk API，](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_aq1_kd1_5cb)查询验证会有所不同。 |
   | [类型行为不匹配](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_djn_x4c_tx) | 处理数据类型与架构中指定的数据类型不同的数据的操作：保留Salesforce返回的数据截断数值以匹配Salesforce模式四舍五入数值以匹配Salesforce模式 |
   | 使用代理服务器                                               | 指定是否使用HTTP代理连接到Salesforce。                       |
   | 代理主机名                                                   | 代理主机。                                                   |
   | 代理端口                                                     | 代理端口。                                                   |
   | 代理需要凭证                                                 | 指定代理是否需要用户名和密码。                               |
   | 代理用户名                                                   | 代理凭据的用户名。                                           |
   | 代理密码                                                     | 代理凭证的密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 使用相互认证                                                 | 在Salesforce中启用后，您可以使用SSL / TLS相互身份验证来连接到Salesforce。默认情况下，在Salesforce中未启用相互身份验证。要启用相互身份验证，请联系Salesforce。在启用相互身份验证之前，必须将[相互身份验证证书](https://help.salesforce.com/articleView?id=security_keys_uploading_mutual_auth_cert.htm&type=0)存储在Data Collector资源目录中。有关更多信息，请参阅[密钥库和信任库配置](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z)。 |
   | [密钥库文件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z) | 密钥库文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。默认情况下，不使用任何密钥库。 |
   | 密钥库类型                                                   | 要使用的密钥库的类型。使用以下类型之一：Java密钥库文件（JKS）PKCS＃12（p12文件）默认值为Java密钥库文件（JKS）。 |
   | 密钥库密码                                                   | 密钥库文件的密码。密码是可选的，但建议使用。**提示：**为了保护敏感信息（如密码），可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 密钥库密钥算法                                               | 用于管理密钥库的算法。默认值为 SunX509。                     |