# Groovy脚本

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310113722032.png) 资料收集器

Groovy脚本编制源运行Groovy脚本来创建Data Collector 记录。Groovy脚本源支持Groovy版本2.4。

该脚本在管道运行期间运行。源可以支持复杂的多线程脚本或简单的单线程脚本。该脚本可以作用于阶段中配置的脚本参数。脚本的基本流程必须执行以下操作：

- 如果支持多线程处理，则创建线程
- 创建批次
- 创建记录
- 将记录添加到批次
- 处理批次
- 管道停止时停止

该脚本必须处理所有必要的处理，例如生成事件，发送错误以进行处理以及在用户停止管道或没有更多数据时停止。您可以从脚本中调用外部Java代码。

要处理重新启动，脚本必须保持偏移量以跟踪原点停止并应重新启动的位置。对于偏移量，脚本需要与唯一值关联的称为实体的键。对于多线程处理，实体必须标识每个线程处理的数据分区。处理批次的方法为每个实体保存一个偏移值。

例如，假设您的脚本使用API读取URL格式为的数据，从而处理有关美国各州的数据`../&page=`。在脚本中，每个线程从一个状态读取数据，直到完成该状态为止。您可以将实体设置为状态，并将偏移量设置为页码。

Origin 提供了广泛的示例代码，可用于开发脚本。

配置原点时，输入脚本和所需的输入，包括批处理大小和线程数，以及脚本中使用的所有脚本参数。

## 脚本对象

Groovy脚本源中的脚本可以使用以下对象：

- 记录

  包含要处理的字段和值的对象。`record`使用`sdc.createRecord()`方法创建新对象 。对象可用的方法取决于原始配置。您可以将记录类型配置为本机对象或数据收集器 记录。

  有关更多信息，请参见[访问记录详细信息](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/GroovyScripting.html#concept_xfd_s1q_l3b)。

- 批量

  收集记录以一起处理的对象。`batch`使用`sdc.createBatch()` 方法创建新 对象。该对象包括以下方法：`add()` -将记录追加到批次中。`add()` -将记录列表追加到批处理中。`addError(,)`-将错误记录追加到批处理中。附加的错误记录包含相关的错误消息。`addEvent()`-将事件追加到批次。在实施事件方法之前，请验证该阶段是否启用了事件生成。`size()` -返回批处理中的记录数。`process(, )` -处理批处理并提交命名实体的偏移量。`getSourceResponseRecords()` -处理批次后，检索下游阶段返回的所有响应记录。

- 日志

  将消息写入log4j日志的对象。使用 `sdc.log`访问配置为舞台的对象。该对象包括与日志文件中的级别相对应的方法：`info(, ...)``warn(, ...)``error(, ...)``trace(, ...)`消息模板可以包含位置变量，用大括号{}表示。在编写消息时，该方法用相应位置的参数替换每个变量-即，方法将第一个{}出现的位置替换为第一个参数，依此类推。

- 直流

  一个包装器对象，用于访问用户脚本可用的常量，方法和对象。

  该`sdc`对象包含以下常量：`lastOffsets`-包含每个实体最后保存的偏移量的字典。在脚本的开头使用，以读取与成功处理的批处理关联的最后一个值。**注意：**在管道运行时，常量不会更新。`batchSize`-单个批次中创建的记录数。使用批处理大小属性在“性能”选项卡上配置。`nThreads`-要同时运行的线程数。在“性能”选项卡上使用“线程数”属性进行配置。`userParams` -词典，其中包含脚本参数和在“高级”选项卡上配置的参数以及“脚本中的参数”属性。

  该`sdc`对象包含以下方法：`createBatch()` -返回新批次。`createRecord()`-返回带有传递的ID的新记录。传递一个唯一标识记录的字符串，该字符串包含足够的信息以跟踪记录源。`isStopped()` -返回一个布尔值，该值指示管道是否已停止。`isPreview()` -返回一个布尔值，该值指示管道是否处于预览模式。`getFieldNull(, )` -返回以下之一：如果该值不为null，则位于指定路径的字段的值为字段类型定义的空对象，例如 `NULL_INTEGER`或 `NULL_STRING`，如果值是null`NULL`如果指定的路径上没有字段，则为未分配的空对象`createMap()` -返回地图，用作记录中的字段。通过 `true`创建列表地图字段，或 `false`创建地图字段。`createEvent(, )`-返回具有指定事件类型和版本的新事件记录。在实施事件方法之前，请验证该阶段是否启用了事件生成。

## 多线程处理

Groovy脚本编写源可以基于“线程数”属性使用多个并发线程来处理数据。

要启用多线程处理，请编写脚本以创建配置的线程数。每个线程必须创建一个批处理，然后通过调用该`batch.process(, )`方法将批处理传递给可用的管道运行器 。管道运行器是无源管道实例 - 管道的实例，包括管道中的所有处理器，执行程序和目的地，并在源之后处理所有管道处理。

每个管道运行程序一次处理一个批处理，就像在单个线程上运行的管道一样。当数据流减慢时，管道运行器会闲置等待，直到需要它们为止，并定期生成一个空批。您可以配置“运行者空闲时间”管道属性来指定间隔或选择退出空批次生成。

多线程管道保留每个批处理中的记录顺序，就像单线程管道一样。但是由于批处理 是由不同的流水线处理程序处理的，因此无法确保将批处理写入目的地的顺序。

例如，假设您编写脚本以使用多个线程以最后修改的时间戳的顺序读取文件，并且将源配置为使用五个线程。启动管道时，原始节点将创建五个线程，而Data Collector 会创建匹配数量的管道运行器。

原点为五个最早的文件分配一个线程。每个线程处理其分配的文件，创建一批数据，并将每批数据传递给管道运行器。

线程完成文件处理后，源将根据上次修改的时间戳将线程分配给下一个文件，直到处理完所有文件为止。

有关多线程管道的更多信息，请参见《[多线程管道概述》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Multithreaded_Pipelines/MultithreadedPipelines.html#concept_zpp_2xc_py)。

## 访问记录详细信息

默认情况下，您使用脚本语言中的本机类型来访问脚本中的记录。但是，对于本机类型，您无法轻松访问Data Collector 记录的所有功能，例如字段属性。

要直接将脚本中的记录作为Data Collector 记录访问，请将“记录类型”高级属性设置为，以配置阶段以使用Data Collector Java API处理脚本中的记录`Data Collector Records`。

在脚本中，从Java包中引用所需的类`com.streamsets.pipeline.api`，然后使用适当的方法访问记录和字段。使用Data Collector Java API，脚本可以访问Data Collector 记录的所有功能。有关完整说明，请参阅[GitHub中](https://github.com/streamsets/datacollector-api/blob/master/src/main/java/com/streamsets/pipeline/api/Record.java#L64)的[Data Collector Java API](https://github.com/streamsets/datacollector-api/blob/master/src/main/java/com/streamsets/pipeline/api/Record.java#L64)。

例如，在脚本中包括以下几行，以使用Data Collector Java API执行以下操作：

- 创建一个名为的字符串字段`new`，并将其值设置为 `new-value`。
- 更新名为的现有字段，以`old`将`attr`属性的值设置 为`attr-value`。

```
import com.streamsets.pipeline.api.Field
...
record.sdcRecord.set('/new', Field.create(Field.Type.STRING, 'new-value'))
record.sdcRecord.get('/old').setAttribute('attr', 'attr-value')
...
```

## 类型处理

使用 Groovy脚本原点时，请注意以下类型信息：

- 空值的数据类型

  您可以将空值与数据类型相关联。例如，如果脚本为Integer字段分配了空值，则该字段将作为具有空值的整数返回给管道。在Groovy代码中使用常量来创建具有空值的特定数据类型的新字段。例如，可以通过将`NULL_STRING`类型常量分配给字段来创建具有空值的新String字段， 如下所示：`record.value['new_field'] = sdc.NULL_STRING`

- 日期字段

  使用String数据类型创建一个新字段，以特定格式存储日期。例如，以下示例代码创建一个新的String字段，该字段使用以下格式存储当前日期 `YYYY-MM-dd`：` // Define a date object to record the current date def date = new Date() def curBatch = sdc.createBatch() for (record in records) { try {   // Create a string field to store the current date with the specified format   record.value['date'] = date.format('YYYY-MM-dd')   // Add record to the current batch   curBatch.add(record) } catch (Exception e) {   // Send record to error  curBatch.addError(record, e.toString()) } } // Process the current batch curBatch.process(entityName, offset.toString()))`

## 事件产生

您可以使用Groovy脚本源 来为事件流生成事件记录。当您希望舞台根据脚本逻辑生成事件记录时，请启用事件生成。

与任何记录一样，您可以将事件记录向下游传递到目标以进行事件存储，也可以传递给可以配置为使用事件的任何执行程序。有关事件和事件框架的更多信息，请参阅[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

生成事件：

1. 在

   常规

   选项卡上，选择

   生产事件

   属性。

   这样可以使用事件输出流。

2. 在脚本中包括

   以下两个方法：

   - ```
     sdc.createEvent(<String type>, <Integer version>)
     ```

      -创建具有指定事件类型和版本号的事件记录。您可以创建新的事件类型或使用现有的事件类型。现有事件类型在其他事件生成阶段中记录。

     事件记录不包含任何记录字段。根据需要生成记录字段。

   - `batch.toEvent()` -用于将事件记录追加到批处理并将事件传递到事件输出流。

### 活动记录

Groovy脚本源生成的事件记录具有与事件相关的标准记录头属性：

| 记录标题属性                 | 描述                                     |
| :--------------------------- | :--------------------------------------- |
| sdc.event.type               | 事件类型，由`sdc.createEvent`方法指定。  |
| sdc.event.version            | 事件版本，由`sdc.createEvent` 方法指定。 |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                 |

## 记录标题属性

Groovy脚本源中的脚本可以创建自定义记录头属性。流水线逻辑可以使用记录头属性来影响数据流。因此，您可以为特定目的创建自定义记录标题属性。有关更多信息，请参见[记录头属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)。

所有记录都包括一组内部记录头属性，这些阶段在处理记录时会自动更新。错误记录还具有其自己的内部标头属性集。您不能在脚本中更改内部标头属性的值。

您可以使用以下记录头变量来处理头属性：

- `record.` -用于返回标头属性的值。
- `record.attributes` -用于返回自定义记录标题属性的映射，或创建或更新特定记录标题属性。

**提示：**使用数据预览可以查看记录中包含的记录标题属性。

## 调用外部Java代码

您可以从Groovy脚本源调用外部Java代码。只需安装外部Java库以使其可用于源。然后，从为原点开发的Groovy脚本中调用外部Java代码。

有关安装其他驱动程序的信息，请参阅 Data Collector 文档 中的“ [安装外部库](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/ExternalLibs.html%23concept_pdv_qlw_ft) ”。

要从Groovy脚本调用外部Java代码，只需将import语句添加到脚本中，如下所示：

```
import <package>.<class name>
```

例如，假设您安装了Bouncy Castle JAR文件以计算SHA-3（安全哈希算法3）摘要。将以下语句添加到脚本中：

```
import org.bouncycastle.jcajce.provider.digest.SHA3
```

有关更多信息，请参见以下StreamSets博客文章： [从Script Evaluators调用外部Java代码](https://streamsets.com/blog/calling-external-java-code-script-evaluators/)。

## 授予Groovy脚本权限

如果Data Collector使用Java安全管理器，并且Groovy代码需要访问网络资源，则必须更新Data Collector安全策略以包括Groovy脚本。

默认情况下启用Java安全管理器。有关更多信息，请参阅Data Collector 文档中的[Java Security Manager](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Configuration/DCEnvironmentConfig.html#concept_tm4_pbg_ht)。

1. 在Data Collector 配置目录中，编辑安全策略：

   ```
   $SDC_CONF/sdc-security.policy
   ```

2. 将以下行添加到文件中：

   ```
   // groovy source code
   grant codebase "file:///groovy/script" { 
     <permissions>;
   };
   ```

   ``您授予Groovy脚本的权限在哪里。

   例如，要授予`/data/files`目录和子目录中所有文件的读取权限 ，请添加以下行：

   ```
   // groovy source code
   grant codebase "file:///groovy/script" { 
     permission java.util.PropertyPermission "*", "read";
     permission java.io.FilePermission "/data/files/*", "read";
   };
   ```

3. 重新启动Data Collector。

## 配置Groovy脚本原点

配置Groovy脚本源，以运行Groovy脚本来创建Data Collector 记录。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/GroovyScripting.html#concept_hn5_tjp_l3b) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **性能”**选项卡上，配置以下属性：

   | 性能属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 批量大小 | 单个批次中要生成的记录数。脚本使用`sdc.batchSize` 常量访问此值并实现批处理。默认值是1000 数据收集器荣誉值高达到数据收集器最大批量大小。该数据采集器默认设置为1000。 |
   | 线程数   | 并行并行生成数据的线程数。脚本使用`sdc.numThreads`常量访问此值， 并实现多线程处理。 |

3. 在“ **脚本”**选项卡上，配置以下属性：

   | 脚本属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 用户脚本 | 在管道执行期间运行的脚本。**提示：**要切换全屏编辑，请在光标位于编辑器中时按F11或Esc（取决于操作系统）。 |

4. 在“ **高级”**选项卡上，配置以下属性：

   | 先进物业                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [记录类型](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/GroovyScripting.html#concept_xfd_s1q_l3b) | 脚本执行期间要使用的记录类型：数据收集器记录-选择脚本何时使用数据收集器 Java API方法访问记录。本机对象-选择脚本何时使用本机类型访问记录。默认值为“本机对象”。 |
   | 脚本中的参数                                                 | 脚本参数及其值。脚本使用`sdc.userParams` 字典访问值。        |