# 运行时值

运行时值是您在管道外部定义的值，用于阶段和管道属性。您可以更改每个管道运行的值，而无需编辑管道。

您可以将运行时值用于任何允许使用表达式语言的管道属性。例如，您可以使用运行时值来表示批处理大小，超时，目录和URI。您不能使用运行时值表示字段。

您可以使用以下方法将运行时值传递给管道：

- 运行时参数

  当您要在启动管道时指定管道属性的值时，请使用运行时参数。

  您在配置管道时定义运行时参数，然后从该管道中调用这些参数。启动管道时，请指定要使用的参数值。

  为单个管道定义了运行时参数-只有该管道才能调用它们。**注意：**在2.5.0.0之前的Data Collector版本中，管道运行时参数称为管道常量。

- 运行时属性

  当您要为文件中的多个管道属性定义值时，请使用运行时属性。

  您可以在Data Collector本地文件中定义运行时属性，然后在管道中调用这些属性。在运行时，Data Collector从文件中加载属性值。运行时属性文件可以包含多个属性。

  为整个数据收集器定义了运行时属性-任何管道都可以调用它们。

- 运行时资源

  当您想将通用管道配置属性存储在具有受限权限的文件中时，请使用运行时资源。

  您可以在Data Collector本地的文件中定义运行时资源，然后在管道中调用这些资源。您可以限制资源文件的权限，但是任何可以创建管道的用户都可以访问存储在文件中的数据。

  **提示：**要更安全地定义敏感值，请使用[凭证存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)。

**注意：**在为StreamSets Control Hub 运行的管道配置运行时值时，所有运行管道的Data Collector必须具有在预期位置本地定义的值。

## 使用运行时参数

运行时参数是您在管道中定义的参数，然后在同一管道中调用。启动管道时，请指定要使用的参数值。启动管道时，请使用运行时参数指定管道属性的值。

使用运行时参数来定义阶段和管道属性的值。例如，您可以定义一个错误目录参数，该参数指向测试计算机和生产计算机上的不同目录。或者，您可以定义一个连接参数，该参数指向测试和生产环境中的原始数据库的不同数据库连接。

定义运行时参数时，请输入要使用的默认值。启动管道时，可以指定另一个值来覆盖默认值。管道运行时，该值将替换运行时参数的名称。

**注意：**如果在 不停止管道的情况下关闭然后重新启动Data Collector，则管道将使用最后一组参数值继续运行。

要实现运行时参数，请执行以下步骤：

1. 定义运行时参数。
2. 在管道中使用表达式来调用运行时参数。

### 步骤1.定义运行时参数

配置管道时定义运行时参数。

1. 在管道属性中，单击“ **参数”** 选项卡。

2. 使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击**添加**图标，然后为每个参数定义名称和默认值。

   例如，定义一个名为JDBCConnectionString的参数，其默认值为`jdbc:mysql://localhost:3306/sample`。

### 步骤2.调用运行时参数

在管道中使用表达式来调用运行时参数。

您可以使用运行时参数来表示允许使用StreamSets 表达式语言的任何阶段或管道属性，包括显示为文本框，复选框或下拉菜单的属性。您还可以在为脚本处理器开发的代码中调用运行时参数。

#### 从文本框呼叫

要在显示为文本框的阶段或管道属性中调用运行时参数，请使用以下语法：

```
${<parameter name>}
```

例如，要将JDBCConnectionString运行时参数用于“ JDBC多表使用者”来源，请为“ JDBC连接字符串”属性输入以下语法：

```
${JDBCConnectionString}
```

只需输入参数名称，就可以从表达式语言函数中调用运行时参数。例如，以下表达式返回JDBCConnectionString运行时参数的值：

```
 ${record:value(JDBCConnectionString)}
```

您可以使用运行时参数来表示属性的一部分。例如，您可以使用RootDir运行时参数，并将目录的其余部分附加到属性中，如下所示：

```
${RootDir}/logfiles
```

#### 从复选框和下拉菜单调用

要在显示为复选框或下拉菜单的阶段或管道属性中调用运行时参数，首先必须将该属性转换为文本框。

单击复选框或下拉菜单旁边的“ **使用参数”**图标（![img](imgs/icon_UseParam.png)），将属性转换为文本框，然后使用所需的语法调用该参数。

例如，下图显示了显示为下拉菜单的“交付保证”属性已转换为文本框，以便可以从该属性中调用参数：

![img](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Graphic/PipelineParamsCheckboxDropDown.png)

该参数必须评估为属性类型的有效选项：

- 选框

  从显示为复选框的属性调用的参数必须取值为true或false。

- 下拉菜单

  从显示为下拉菜单的属性调用的参数必须计算为有效的键值。菜单中的每个选项都有一个关联的键值。

  例如，使用参数为交付保障属性，显示为一个下拉菜单，参数的值必须为有效的键值中的一个，AT_LEAST_ONCE或者 AT_MOST_ONCE，而不是菜单选项之一， At Least Once或At Most Once。

  要查看菜单的有效键，请使用所需选项配置管道，然后导出管道并查看导出的管道JSON文件。

#### 从脚本处理器调用

您可以在为脚本处理器开发的代码中调用运行时参数。

用于调用运行时参数的方法取决于以下脚本处理器类型：

- JavaScript评估程序或Jython评估程序处理器

  在任何处理器脚本中使用以下语法： `${}`。例如，以下JavaScript代码行将NewFieldValue参数的值分配给地图字段：`records[i].value.V= ${NewFieldValue}`

- Groovy评估器处理器

  `sdcFunctions.pipelineParameters()`在任何处理器脚本中使用该方法可返回为管道定义的所有运行时参数的映射。例如，以下Groovy代码行将CompanyParam参数的值分配给“公司名称”字段：`record.value['Company Name'] = sdcFunctions.pipelineParameters()['CompanyParam']`

## 使用运行时属性

运行时属性是您在Data Collector本地文件中定义并在管道中调用的属性。使用运行时属性，可以为不同的Data Collector定义不同的值集。

使用运行时属性来定义阶段和管道属性的值。例如，您可以定义一个错误目录运行时属性，该属性指向测试计算机和生产计算机上的不同目录。同样，您可以为原始阶段和目标阶段创建测试和生产运行时属性。

定义运行时属性时，可以使用静态值或环境变量。

调用运行时属性时，可以将其用作较大的属性定义的一部分。例如，您可以将运行时属性设置为HOME环境变量，该变量在不同的计算机上会有所不同，然后将运行时属性用作较长目录的基本目录。

要实现运行时属性，请执行以下步骤：

1. 定义运行时属性。
2. 在管道中使用表达式来调用运行时属性。

### 步骤1.定义运行时属性

您可以在Data Collector 配置文件`sdc.properties`或单独的运行时属性文件中定义运行时属性。

如果在单独的运行时属性文件中定义属性，请对安装类型使用必需的过程。

- 数据收集器 配置文件

  使用以下步骤在Data Collector 配置文件中定义运行时属性：在`$SDC_CONF/sdc.properties`文件中，如下配置 **runtime.conf.location**属性：`runtime.conf.location=embedded`要在`$SDC_CONF/sdc.properties` 文件中定义运行时属性，请使用以下两种格式之一：要将值用于运行时属性，请使用以下格式：`runtime.conf_=`例如，以下运行时属性定义了Hadoop FS目录模板：`runtime.conf_HDFSDirTemplate=/HDFS/DirectoryTemplate`要将环境变量用于运行时属性，请使用以下格式：`runtime.conf_=${env("")}`例如，以下运行时属性定义一个基本目录，并将其设置为HOME环境变量：`runtime.conf_BaseDir=${env("HOME")}`重新启动Data Collector 以启用更改。

- RPM和tarball的独立运行时属性文件

  使用以下步骤在单独的运行时属性文件中为RPM或tarball安装定义运行时属性：创建一个文本文件，并将其保存在相对于该 `$SDC_CONF`目录的目录中。要在单独的文本文件中定义运行时属性，请使用以下两种格式之一：要将值用于运行时属性，请使用以下格式：`=`例如，以下运行时属性定义了Hadoop FS目录模板：`HDFSDirTemplate=/HDFS/DirectoryTemplate`要将环境变量用于运行时属性，请使用以下格式：`=${env("")}`例如，以下运行时属性定义一个基本目录，并将其设置为HOME环境变量：`BaseDir=${env("HOME")}`在Data Collector 配置文件中，`$SDC_CONF/sdc.properties`将**runtime.conf.location**属性配置 为指向单独的运行时属性文件的相对位置。例如，以下单独的运行时属性文件位于`runtime`相对于该`$SDC_CONF` 目录的目录中：`runtime.conf.location=runtime/test-runtime.properties`重新启动Data Collector 以启用更改。

- Cloudera Manager的单独的运行时属性文件

  使用以下步骤在用于Cloudera Manager安装的单独的运行时属性文件中定义运行时属性：创建文本文件，并使用以下两种格式之一在文本文件中定义运行时属性：要将值用于运行时属性，请使用以下格式：`=`例如，以下运行时属性定义了Hadoop FS目录模板：`HDFSDirTemplate=/HDFS/DirectoryTemplate`要将环境变量用于运行时属性，请使用以下格式：`=${env("")}`例如，以下运行时属性定义一个基本目录，并将其设置为HOME环境变量：`BaseDir=${env("HOME")}`将文本文件保存在运行Data Collector的每个节点上的同一目录中。在Cloudera Manager中，选择**StreamSets**服务，然后单击**Configuration**。在“ **配置”**页面上的**“ sdc-env.sh**的**数据收集器高级配置代码段（安全阀）”**字段中，添加以下行以定义运行时属性文件目录：`ln -sf //runtime.properties "${CONF_DIR}/runtime.properties"`例如：`ln -sf /opt/sdc-runtime/runtime.properties "${CONF_DIR}/runtime.properties"`在**sdc.properties**的**Data Collector高级配置代码片段（安全阀）**字段中，通过添加以下行，将**runtime.conf.location**属性配置 为指向单独的运行时属性文件：`runtime.conf.location=runtime.properties `重新启动Data Collector 以启用更改。

有关更多信息，请参阅[数据收集](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html)器 文档 中的[配置数据收集](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html)器。

### 步骤2.调用运行时属性

使用该`runtime:conf`函数调用运行时属性。您可以使用运行时属性来表示允许使用表达式语言的任何阶段或管道属性。

要调用运行时属性，请使用以下语法：

```
${runtime:conf(<property name>)}
```

**注意：**如果您在Data Collector 配置文件中定义了运行时属性，请输入just ``和not `runtime.conf_`。

例如，要将HDFSDirTemplate运行时属性用于Hadoop FS目标，请为目录模板属性输入以下语法：

```
${runtime:conf('HDFSDirTemplate')}
```

您可以使用运行时属性来表示属性的一部分。例如，您可以使用RootDir运行时属性，并在该属性中附加目录的其余部分，如下所示：

```
${runtime:conf('RootDir')}/logfiles
```

## 使用运行时资源

与运行时属性类似，运行时资源是您在Data Collector本地文件中定义并在管道中调用的值。但是使用运行时资源，您可以限制文件的权限以保护信息。

使用运行时资源来存储多个管道的通用配置属性，例如外部系统的URL。请注意，任何可以创建管道的用户都可以访问存储在资源文件中的数据。

**提示：**要更安全地定义敏感值，请使用[凭证存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)。

要实现运行时资源，请执行以下步骤：

1. 定义每个运行时资源。
2. 在管道中使用表达式来调用运行时资源。

### 步骤1.定义运行时资源

使用以下步骤定义运行时资源：

1. 对于每个资源，创建一个文本文件并将其保存在

   ```
   $SDC_RESOURCES
   ```

    目录中。

   文件必须包含一个在调用资源时要使用的信息。

2. （可选）限制文件的权限。

   通常，任何人都可以读取文件。要限制权限，请配置文件，以便只有所有者才具有文件的读写权限（八进制数600或400）。所有者必须是运行Data Collector的系统用户。

   在管道中使用资源时，请指定是否限制文件。

### 步骤2.调用运行时资源

使用`runtime:loadResource`或 `runtime:loadResourceRaw`函数来调用运行时资源。您可以使用运行时资源在允许使用表达式语言的任何阶段或管道属性中表示信息。

**注意：**在大多数情况下，您将使用该`runtime:loadResource`功能来修剪文件中的任何前导或尾随空白字符。但是，如果需要，您还可以使用该`runtime:loadResourceRaw`功能，该功能在文件中包含任何前导或尾随空格字符。

要调用运行时资源，请使用以下语法：

```
${runtime:loadResource(<file name>, <restricted: true | false>)}
```

例如，以下表达式返回`JDBC.txt`文件的内容，并 修剪所有前导或尾随空白字符。该文件包含连接字符串，并且受到限制，因此只有所有者才能读取该文件：

```
${runtime:loadResource("JDBC.txt", true)}
```