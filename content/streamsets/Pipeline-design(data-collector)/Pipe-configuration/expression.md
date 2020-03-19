# 表达式配置

使用表达式语言可在处理器中配置表达式和条件，例如表达式评估器或流选择器。一些目标属性还允许使用表达式语言，例如Hadoop FS目标的目录模板。

您可以使用表达式语言来定义表示数字或字符串值的任何阶段或管道属性。您还可以使用字段路径表达式来选择要在某些处理器中使用的字段。

使用[表达式完成](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/ExpressionLanguage_overview.html#concept_tns_krz_sr)功能可以确定可以在哪里使用表达式以及可以在该位置使用的表达式元素。

您可以在表达式中使用以下元素：

- 常数
- 日期时间变量
- 栏位名称
- 功能
- 文字
- 经营者
- 运行时参数
- 运行时属性
- 运行时资源

## 基本语法

在所有表达式前加一个美元符号，并用大括号括起来，如下所示：`${}`。

例如，要添加2 + 2，请使用以下语法：$ {2 + 2}。

## 在表达式中使用字段名称

当管道对预览有效时， [表达式完成](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/ExpressionLanguage_overview.html#concept_tns_krz_sr)将在列表中提供可用的字段名称。当列表不可用时，请为字段名称使用适当的格式。

在表达式中使用字段名称时，请使用以下语法：

```
${record:value("/<field name>")}
```

**注意：**您可以使用单引号或双引号将字段名称引起来。

例如，以下表达式都将DATE字段中的值与TIME字段中的值连接在一起：

```
${record:value('/DATE')} ${record:value('/TIME')}
${record:value("/DATE")} ${record:value("/TIME")}
```

### 具有特殊字符的字段名称

您可以使用引号和反斜杠字符来处理字段名称中的特殊字符。

[表达式完成](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/ExpressionLanguage_overview.html#concept_tns_krz_sr)为带有特殊字符的字段名称提供正确的语法。但是，当您需要手动输入字段名称时，请确保使用以下准则：

- 在带有特殊字符的字段名称周围使用引号

  当字段名称包含特殊字符时，请使用单引号或双引号将字段名称引起来，如下所示：`/""`一些例子：`/"Stream$ets" /'city&state' /"product names"`

- 当使用多组引号时，请在类型之间交替

  在整个表达语言中，使用引号时，可以使用单引号或双引号。但是在嵌套引号时，请确保在类型之间交替。

  例如：

  `${record:value('/"Stream$ets"'} ${record:value("/'city&state'"}`

- 使用反斜杠作为转义字符

  要在字段名称中使用引号或反斜杠，请使用反斜杠（\）。

  根据需要添加其他反斜杠以转义引号。

  例如，要将名为“ ID”的字段用作必填字段，则可以使用单个反斜杠：`/ID\'s`

  要在表达式中使用相同的字段，可能需要如下附加的反斜杠：`${record:value('/ID\\'s')}`

## 引用字段名称和字段路径

当管道对预览有效时，通常可以从列表中选择字段。当列表不可用或定义新的字段名称时，您需要使用适当的格式作为字段名称。

要引用字段，请指定字段的路径。字段路径使用类似于目录中文件的语法描述记录中的数据元素。字段路径的复杂度根据记录中的数据类型而有所不同：

- 简单地图或JSON对象

  对于简单的映射或JSON对象，字段是从根目录删除的一级。请参考以下字段：`/`因此，要引用简单JSON对象中的CITY字段，请输入`/CITY`。调用该字段的简单表达式可能如下所示：`${record:value('/CITY')}`

- 复杂的地图或JSON对象

  要引用复杂映射或JSON对象中的字段，请包括该字段的路径，如下所示：`//`例如，以下字段路径描述了一个JSON对象中几层深处的employeeName字段： `/region/division/group/employeeName`。调用该字段的表达式可能如下所示：`${record:value("/region/division/group/employeeName")}`

- 数组或列表

  要引用数组或列表中的字段，请包括该字段的索引和路径，如下所示：`[]//`

  例如，以下字段路径描述了数组中第三个区域索引中的同一employeeName字段： `[2]/east/HR/employeeName`。

  调用该字段的表达式可能如下所示：`${record:value('[2]/east/HR/employeeName')}`

  分隔的记录可以构造为列表。有关更多信息，请参见定界[数据根字段类型](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Delimited.html#concept_zcg_bm4_fs)。

- 文本

  要在记录为一行文本时引用文本，请使用以下字段名称：`/text`

### 通配符用于数组和映射

在某些处理器中，可以将星号通配符（*）用作数组中的索引或映射中的键值。使用通配符可帮助定义映射和数组的字段路径。

您可以按如下所示使用星号通配符：

- [*]

  匹配数组中指定索引的所有值。例如，以下字段路径表示每个部门中每个雇员的社会保险号：`/Division[*]/Employee[*]/SSN`

- / *

  匹配映射中指定键的所有值。例如，以下字段路径表示第一部门中的所有员工信息：`/Division[0]/Employee[*]/*`

## 场路径表达式

您可以在某些处理器中使用字段路径表达式来确定希望处理器使用的字段集。 使用某些保护方法（例如[Expression Evaluator）](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/ProtectionMethods/Method-ExpressionEval.html#concept_gzt_skt_kfb)时，也可以在[Data Protector](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/DataProtector/DataProtector-Overview.html#concept_ws1_w2b_v2b)保护过程中使用字段路径表达式。

例如，您想使用Field Remover处理器删除所有以相同前缀开头的字段。您可以使用字段路径表达式来指定要删除的字段，而不是手动输入每个字段名称。

### 支持阶段

您可以使用字段路径表达式来指定要在以下处理器中使用的字段：

- 野战哈希处理器
- 场掩蔽处理器
- 场消除器处理器
- 现场替换处理器
- 场类型转换器处理器
- 值替换处理器（不建议使用）

### 字段路径表达式语法

创建字段路径表达式时，可以结合使用标准表达式语言语法和字段路径表达式语法。您可以在字段路径表达式中使用以下组件：

- 根域和相对路径

  与指定任何字段路径一样，以斜杠（/）开头字段路径表达式以指示字段相对于根字段的位置。然后，继续定义适当的字段路径。

  例如，以下字段路径表达式使用通配符指定记录中的所有字段：`/*`

- 通配符

  您可以将星号字符（*）和问号字符（？）用作通配符，如下所示：使用星号通配符表示一个或多个字符。例如，要对“商店”地图字段中的所有字段执行操作，可以使用以下字段路径表达式：`/Stores/*`使用问号通配符可以精确地表示一个字符。例如，以下表达式包括所有带有两个字符前缀和下划线的字段：`/??_*`

- 位置谓词括号

  您可以根据字段在列表字段中的位置来指定它。在列表字段的名称之后，指定由方括号（[]）包围的位置。请注意，位置编号从0开始。

  例如，以下表达式调用颜色列表字段中的第四项：`/colors[3]`

- 复杂表达式的括号

  您可以配置使用函数（通常是字段函数）的字段路径表达式，以定义要返回的特定字段子集。配置复杂表达式时，请用方括号（[]）包围表达式，如下所示：

  `/*[${}]`

  例如，以下表达式返回所有将“ info”字段属性设置为任何值的字段：`/*[${f:attribute('info') == '*'}]`

- 现场功能

  使用字段函数可以根据与字段相关的信息来确定要使用的字段，例如字段`f:type`的数据类型，字段 `f:value`的值或字段 `f:attribute`的属性或属性值。

  例如，可以使用字段类型转换器处理器使用以下表达式转换所有Integer字段：`/*[${f:type() == 'INTEGER'}]`

  有关字段函数的更多信息，请参见[字段函数](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/Functions.html#concept_gfs_w55_3cb)。

- 其他功能

  您可以将其他功能（例如记录，字符串或时间功能）用作复杂字段路径表达式的一部分。

  例如，以下表达式定义了将区域属性设置为storeId字段结果的字段子集：`/*[${f:attribute('region') == record:value('/storeId')}]`

## 数据类型强制

当表达式需要时，表达式语言将尝试隐式数据类型转换-称为数据类型强制。当无法强制执行时，Data Collector会将错误记录传递到阶段以进行错误处理。

例如，您有一个Expression Evaluator阶段，该阶段配置为将错误记录发送到管道以进行错误处理，并且管道将错误记录写入文件。表达式计算器包括一个将字符串数据视为整数的表达式。当字段包含整数或有效数字数据时，表达式语言将强制数据类型。如果该字段包含日期，则将该记录写入错误记录文件。

为避免强制错误，您可以在管道中更早地使用字段类型转换器将数据转换为适当的数据类型。