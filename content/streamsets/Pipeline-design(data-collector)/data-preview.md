# 资料预览

## 数据预览概述

您可以预览数据以帮助构建或微调管道。使用Control Hub时，还可以在开发管道片段时使用数据预览。

当您的Control Hub 组织 启用了Data Protector时，您可以使用数据预览来查看分类规则和选定的保护策略如何识别和保护管道中的敏感数据。有关在 启用Data Protector的情况下使用数据预览的更多信息，请参阅第[155页上的“预览数据”](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/DataProtector/ImplementingDataProtection.html#concept_rpv_lq4_bfb)。如果要快速查看规则如何对一组测试数据进行[分类](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/ClassificationRules/Rules-Preview.html#concept_vmq_5mz_jjb)，请尝试[分类预览](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/ClassificationRules/Rules-Preview.html#concept_vmq_5mz_jjb)。

**重要：**策略选择是“ [技术预览”](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/GettingStarted/TechPreview.html#concept_o1l_1sn_kjb)功能，不要期望生产级的质量。

您可以对完整或不完整的管道和片段使用数据预览。您可以从几个选项中进行选择，以提供预览的源数据。

预览数据时，源数据会通过管道或片段，从而允许您查看数据如何在每个阶段传递和更改。您可以编辑舞台属性并再次运行预览，以查看您的更改如何影响数据。您还可以编辑预览数据以测试和调整管道逻辑。

您可以一次预览一个阶段或一组阶段的数据。您还可以在列表视图或表视图中查看数据，并刷新预览数据。

### 数据预览可用性

您可以预览完整和不完整的管道以及Control Hub 管道片段。数据预览可用时，数据预览图标将变为活动状态。

您可以在以下情况下预览数据：

- 在[创作的数据收集器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/PipelineDesigner-Tips.html#concept_yk2_zfz_1cb__authoringSDC)是可用的注册数据采集器。
- 管道中的所有阶段均已连接
- 定义所有必需的属性

**提示：**阶段配置不必精确或完整即可预览数据。连接所有阶段之后，可以通过输入所需属性的任何有效值来启用数据预览。

### 数据预览的源数据

您可以使用以下类型的数据进行数据预览：

- 原始数据-使用原始数据。
- 来自[测试源的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/TestOrigin.html#concept_sgt_s5v_g2b)数据-使用管道或片段属性中配置的来自测试源的数据。

### 写给目的地和执行人

由于数据预览是一种开发工具，因此默认情况下，它不会将数据写入目标系统或将数据传递给连接到目标阶段的执行程序。

数据预览也不会显示管道中目标写入的数据。但是，您可以查看传递到目标阶段的数据，该数据通常类似于写入目标系统的数据。

如果愿意，可以配置预览以将数据写入目标系统并触发连接到目标阶段的执行程序。例如，在测试Data Protector写入策略时，您可以写入目标系统，以查看该策略如何保护测试数据。或者，您可以启用写入执行程序以验证其是否按预期执行了已配置的任务。

要写入目标系统和连接的执行程序，请在“预览配置”对话框中，选择“写入目标和执行程序”。

**重要提示：**建议不要将预览数据写入生产目标系统。

### 笔记

预览数据时，请牢记以下注意事项：

- 日期，日期时间和时间数据- 

  数据预览使用浏览器区域设置的默认格式显示日期，日期时间和时间数据。例如，如果浏览器使用en_US语言环境，则预览将使用以下格式显示日期：MMM d，yh：mm：ss a。

  数据预览使用您在预览配置中选择的时区显示日期，日期时间和时间数据。默认情况下，数据预览使用浏览器时区显示数据。

- Oracle CDC客户端管道-预览使用Oracle CDC客户端源的管道时，数据预览可能会在连接到源系统之前超时。发生这种情况时，请尝试将超时增加到120,000毫秒，以允许连接原始时间。

- 整个文件数据格式-预览处理整个文件数据的管道时，数据预览仅显示一条记录。

## 预览代码

数据预览针对不同类型的数据显示不同的颜色。预览还使用其他代码和格式突出显示更改的字段。

下表描述了颜色和星号编码：

| 预览码       | 描述                   |
| :----------- | :--------------------- |
| 黑色值       | 日期数据               |
| 蓝色值       | 数值数据               |
| 绿色价值     | 字符串数据             |
| 红色值       | 布尔数据               |
| 星号         | 包含已编辑字段值的记录 |
| 红色斜体标签 | 包含已编辑数据的字段   |
| 浅红色背景   | 阶段删除的字段         |
| 斜体值       | 编辑数据               |
| 绿色舞台     | 多阶段预览中的第一阶段 |
| 红色舞台     | 多阶段预览的最后阶段   |

## 预览一个阶段

您可以预览单个阶段的数据。在“预览”面板中，您可以查看每个记录的值，以确定阶段是否按预期方式转换了数据。

1. 在管道画布上方，单击“ **预览”**图标： ![img](imgs/icon_Preview.png)。

   如果禁用了“ **预览”**图标，请检查“ **验证错误”**列表中是否存在未连接的阶段以及未定义的必需属性。

2. 在“ **预览配置”**对话框中，配置以下属性：

   | 预览属性             | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | 预览源               | 预览的源数据：配置的源-提供来自源系统的数据。测试原点-提供来自为管道配置的[测试原点的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/TestOrigin.html#concept_sgt_s5v_g2b)数据。 |
   | 时区                 | 用于显示日期，日期时间或时间数据的时区。默认为浏览器时区。   |
   | 预览批次大小         | 预览中使用的记录数。荣誉值最高为Data Collector预览批处理大小。默认值为10。DataCollector的默认值为10。 |
   | 预览超时             | 等待预览数据的毫秒数。用于限制数据预览等待数据到达原点的时间。仅与瞬时原点相关。 |
   | 写给目的地和执行人   | 确定预览是将数据传递到目的地还是执行者。默认情况下，不将数据传递到目的地或执行者阶段。 |
   | 执行管道生命周期事件 | 触发任何适当的管道事件（通常是Start事件）的生成。如果将事件配置为使用，则也会触发事件使用。 |
   | 显示记录/字段标题    | 在列表视图中时显示记录标题属性和字段属性。属性不会显示在“表”视图中。 |
   | 显示栏位类型         | 在列表视图中显示字段的数据类型。字段类型不显示在“表”视图中。 |
   | 记住配置             | 存储当前的预览配置，以供您在每次请求此管道的预览时使用。运行数据预览后，可以通过选择“预览配置”图标（![img](imgs/icon_PrevPreviewConfig.png)）并清除该选项来在“预览”面板中更改此选项。该更改将在您下次运行数据预览时生效。 |
   | 阅读政策             | 阅读用于预览的保护策略。使用所选策略启用预览数据的分类和保护。**重要：**策略选择是“ [技术预览”](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/GettingStarted/TechPreview.html#concept_o1l_1sn_kjb)功能，不要期望生产级的质量。在启用了[Data Protector的](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/DataProtector/DataProtector-Overview.html#concept_ws1_w2b_v2b)组织中可用。 |
   | 写政策               | 写保护策略以用于预览。使用所选策略启用预览数据的分类和保护。要使用所选策略查看写保护，请启用“写入目的地和执行者”属性，并查看写入管道目的地的数据。**重要：**策略选择是“ [技术预览”](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/GettingStarted/TechPreview.html#concept_o1l_1sn_kjb)功能，不要期望生产级的质量。在启用了[Data Protector的](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/DataProtector/DataProtector-Overview.html#concept_ws1_w2b_v2b)组织中可用。 |

3. 点击**运行预览**。

   “预览”面板突出显示原始阶段，并在列表视图中显示预览数据。由于这是管道的起点，因此不会显示任何输入数据。

   要以表格视图查看预览数据，请单击**表格视图**图标：![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_PrevTableView.png)。

4. 要查看下一阶段的数据，请在管道画布中选择该阶段。

5. 要刷新预览，请点击**重新加载预览**。

   刷新预览将提供一组新的数据。

6. 要退出数据预览，请点击**关闭预览**。

## 预览多个阶段

您可以预览管道中一组链接的阶段的数据。

预览多个阶段时，请选择组中的第一个阶段和最后一个阶段。然后，“预览”面板显示组中第一阶段的输出数据和组中最后阶段的输入数据。

在“预览”面板中，您可以查看每个记录的值以确定阶段组是否按预期方式转换了数据。

1. 在管道画布上方，单击“ **预览”**图标： ![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_Preview.png)。

   如果禁用了“ **预览”**图标，请检查“ **验证错误”**列表中是否存在未连接的阶段以及未定义的必需属性。

2. 在“ **预览配置”**对话框中，配置以下属性：

   | 预览属性             | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | 预览源               | 预览的源数据：配置的源-提供来自源系统的数据。测试原点-提供来自为管道配置的[测试原点的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/TestOrigin.html#concept_sgt_s5v_g2b)数据。 |
   | 时区                 | 用于显示日期，日期时间或时间数据的时区。默认为浏览器时区。   |
   | 预览批次大小         | 预览中使用的记录数。荣誉值最高为Data Collector预览批处理大小。默认值为10。DataCollector的默认值为10。 |
   | 预览超时             | 等待预览数据的毫秒数。用于限制数据预览等待数据到达原点的时间。仅与瞬时原点相关。 |
   | 写给目的地和执行人   | 确定预览是将数据传递到目的地还是执行者。默认情况下，不将数据传递到目的地或执行者阶段。 |
   | 执行管道生命周期事件 | 触发任何适当的管道事件（通常是Start事件）的生成。如果将事件配置为使用，则也会触发事件使用。 |
   | 显示记录/字段标题    | 在列表视图中时显示记录标题属性和字段属性。属性不会显示在“表”视图中。 |
   | 显示栏位类型         | 在列表视图中显示字段的数据类型。字段类型不显示在“表”视图中。 |
   | 记住配置             | 存储当前的预览配置，以供您在每次请求此管道的预览时使用。运行数据预览后，可以通过选择“预览配置”图标（![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_PrevPreviewConfig.png)）并清除该选项来在“预览”面板中更改此选项。该更改将在您下次运行数据预览时生效。 |
   | 阅读政策             | 阅读用于预览的保护策略。使用所选策略启用预览数据的分类和保护。**重要：**策略选择是“ [技术预览”](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/GettingStarted/TechPreview.html#concept_o1l_1sn_kjb)功能，不要期望生产级的质量。在启用了[Data Protector的](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/DataProtector/DataProtector-Overview.html#concept_ws1_w2b_v2b)组织中可用。 |
   | 写政策               | 写保护策略以用于预览。使用所选策略启用预览数据的分类和保护。要使用所选策略查看写保护，请启用“写入目的地和执行者”属性，并查看写入管道目的地的数据。**重要：**策略选择是“ [技术预览”](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/GettingStarted/TechPreview.html#concept_o1l_1sn_kjb)功能，不要期望生产级的质量。在启用了[Data Protector的](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/DataProtector/DataProtector-Overview.html#concept_ws1_w2b_v2b)组织中可用。 |

3. 点击**运行预览**。

   “预览”面板突出显示原始阶段，并在列表视图中显示预览数据。由于这是管道的起点，因此不会显示任何输入数据。

   要以表格视图查看预览数据，请单击**表格视图**图标：![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_PrevTableView.png)。

4. 要预览多个阶段，请单击“ **多个”**。

   预览画布突出显示了第一阶段和最后阶段，如下所示：

   ![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/PDesignerMultiStagePreview.png)

   “预览”面板显示所选阶段组的输入和输出数据。您可以查看每个记录的详细信息。

5. 要更改组中的第一阶段，请选择当前的第一阶段，然后选择所需的阶段。

   例如，假设您正在预览上图中显示的管道。要更改第一阶段，请选择目录1（当前的第一阶段），然后选择所需的第一阶段，例如Field Masker 1。

6. 要更改组中的最后一个阶段，请选择当前的最后一个阶段，然后选择所需的阶段。

7. 要刷新预览，请点击**重新加载预览**。

   刷新预览将提供一组新的数据。

8. 要退出数据预览，请点击**关闭预览**。

## 编辑预览数据

您可以编辑预览数据以查看一个阶段或一组阶段如何处理更改的数据。编辑预览数据以测试可能不会出现在预览数据集中的数据条件。

例如，当阶段根据表达式过滤整数数据时，您可以更改输入数据以测试正整数值和负整数值以及零。

您可以在以下位置编辑预览数据：

- 原点的输出数据列。
- 处理器的输入数据列。

编辑预览数据时，可以将更改的数据通过管道传递，也可以还原更改以返回到原始数据。

1. 要更改字段值，请在原点的“ **输出数据”**列或所有其他阶段的“ **输入数据”**列中，单击要更改的值，然后输入新值。

   您可以编辑任何输入数据的值。

2. 要处理更改的数据，请单击**“运行更改”**。

   这将使用当前数据集和阶段配置来运行数据预览。

   在“输入数据”列中，具有更改值的记录显示一个星号，并突出显示更改的值。输出数据列显示处理结果。您可以根据需要经常更改和处理预览数据。

3. 要刷新预览，请点击**重新加载预览**。

   刷新预览将提供一组新的数据。

4. 要还原对数据的更改，请点击**还原数据更改**。

## 编辑属性

在数据预览中，您可以编辑舞台属性以查看更改如何影响预览数据。例如，您可以在“表达式计算器”中编辑表达式以查看表达式如何更改数据。

编辑属性时，可以使用现有的预览数据测试更改，也可以刷新预览数据。

更改原点中的属性时，刷新预览数据以测试更改。通过刷新预览数据，数据收集器 可以使用最新的原始属性来处理预览数据，而不是使用缓存的数据。

**注意：**与数据更改不同，您无法自动还原属性更改。手动还原您不想保留的所有更改。

1. 要在数据预览中编辑舞台属性，请选择要编辑的**舞台**，然后单击“ **舞台配置”**图标：![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_PrevStageConfig.png)。

2. 根据需要更改属性。

3. 要测试原点中更改的属性，请单击“ **重新加载预览”**。

   这将刷新预览数据。根据原始类型，它可能使用相同的数据或具有更新的原始属性的一组新数据。

   要使用相同的数据集在任何非原始阶段中测试属性，请点击 **更改运行**。

4. 如果要还原更改，请手动将属性改回。