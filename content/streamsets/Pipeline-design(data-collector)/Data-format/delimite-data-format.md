# 定界数据格式

Data Collector可以读写定界数据。

## 读取定界数据

读取定界数据的源会为文件，对象或消息中的每个定界行生成一条记录。处理定界数据的处理器会生成记录，如处理器概述中所述。

您可以按以下格式读取定界数据：

- **默认CSV-**包含逗号分隔值的文件。忽略文件中的空行。
- **RFC4180 CSV-**严格遵循RFC4180准则的逗号分隔文件。
- **MS Excel CSV** -Microsoft Excel逗号分隔文件。
- **MySQL CSV** -MySQL逗号分隔文件。
- **制表符分隔的值** -包含制表符分隔的值的文件。
- **PostgreSQL CSV** -PostgreSQL逗号分隔文件。
- **PostgreSQL文本** -PostgreSQL文本文件。
- **自定义** -使用用户定义的定界符，转义符和引号字符的文件。
- **多字符**定界-使用多个用户定义的字符定界字段和行以及单个用户定义的转义和引号字符的文件。

您可以将列表或列表映射根字段类型用于定界数据，并且可以选择在标题行中包括字段名称（如果有）。

使用标题行时，可以启用带有其他列的记录处理。其他列使用自定义的前缀和顺序递增的顺序整数，如命名 `_extra_1`， `_extra_2`。当您禁止其他列时，包含其他列的记录将发送到错误。

您也可以将字符串常量替换为空值。

当一条记录超过为该阶段定义的最大记录长度时，基于消息的源和处理器将根据为该阶段配置的错误处理来处理该对象。

当记录超过最大长度时，基于文件的来源将无法继续读取文件。已经从文件中读取的记录将传递到管道。然后，原点的行为基于为该阶段配置的错误处理。

有关处理定界数据的阶段的列表，请参见[按阶段的数据格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-DataFormats/DataFormat_Title.html#concept_jn1_nzb_kv)。

### 定界数据根字段类型

从定界数据创建的记录可以对根字段使用列表或列表映射数据类型。

当源或处理器为定界数据创建记录时，它们将创建指定类型的单个根字段，并将定界数据写入该根字段中。

使用默认的列表映射根字段类型可以轻松处理定界数据。

- 列表图

  轻松使用表达式中的字段名称或列位置。建议用于所有新管道。

  列表映射根字段类型导致保留数据顺序的结构，如下所示：`/: /: /: ...`

  例如，对于列表映射根字段类型，以下定界行：`TransactionID,Type,UserID 0003420303,04,362 0003420304,08,1008`

  转换为记录如下：`/TransactionID: 0003420303 /Type: 04 /UserID: 362 /TransactionID: 0003420304 /Type: 08 /UserID: 1008`

  如果数据不包含标题，或者您选择忽略标题，列表映射记录将列位置用作标题，如下所示：`0:  1:  2: `

  例如，当您忽略相同数据的标题时，将获得以下记录：`0: 0003420303 1: 04 2: 362 0: 0003420304 1: 08 2: 1008`

  在表达式中，可以将字段名称或列位置与标准记录功能一起使用来调用字段。例如，您可以使用以下任一record：value（）表达式在TransactionID字段中返回数据：`${record:value('/TransactionID')} ${record:value('[0]'}`

  **注意：**在为脚本处理器（如Jython Evaluator或JavaScript Evaluator）编写脚本时，应将列表映射记录视为映射。有关标准记录功能的更多信息，请参见[记录功能](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/Functions.html#concept_p1z_ggv_1r)。

- 清单

  为版本1.1.0之前创建的管道提供持续支持。不建议用于新管道。

  列表根字段类型会在list中产生带有标头位置的索引以及带有每个标头和关联值的映射，如下所示：`0   /header =    /value =  1   /header =    /value =  2     /header =    /value =  ...`

  例如，上述相同的定界行将转换为记录，如下所示：`0   /header = TransactionID   /value = 0003420303 1   /header = Type   /value = 04 2   /header = UserID   /value = 362 0   /header = TransactionID   /value = 0003420304 1   /header = Type   /value = 08 2   /header = UserID   /value = 1008`

  如果数据不包含标题，或者您选择忽略标题，则列表记录会如下所示从映射中省略标题：`0   /value =  1   /value =  2   /value =  ...`

  例如，当您忽略相同示例数据的标题时，将获得以下记录：`0   /value = 0003420303 1   /value = 04 2   /value = 362 0   /value = 0003420304 1   /value = 08 2   /value = 1008`

  对于列表记录中的数据，您应该使用定界数据功能或在标准记录功能中包括完整字段路径。例如，您可以使用record：dValue（）分隔数据函数来返回与指定标头关联的值。

  **提示：**可以使用record：dToMap（）函数将列表记录转换为地图，然后使用标准函数进行记录处理。有关record：dToMap以及定界数据记录函数及其语法的完整列表的更多信息，请参见定界[数据记录函数](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/Functions.html#concept_s2c_q14_fs)。对于支持这种数据格式起源的完整列表，请参阅[起源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-DataFormats/DataFormat_Title.html#concept_kgd_11c_kv) “数据格式的舞台”附录。

## 写定界数据

处理定界数据时，基于文件或对象的目标会将每个记录写为文件或对象中的定界行。基于消息的目标将每个记录写为一条消息。处理器写入处理器概述中指定的定界数据。

目标将记录写为定界数据。使用此数据格式时，根字段必须是list或list-map。

您可以按以下格式写定界数据：

- **默认CSV-**包含逗号分隔值的文件。忽略文件中的空行。
- **RFC4180 CSV-**严格遵循RFC4180准则的逗号分隔文件。
- **MS Excel CSV** -Microsoft Excel逗号分隔文件。
- **MySQL CSV** -MySQL逗号分隔文件。
- **制表符分隔的值** -包含制表符分隔的值的文件。
- **PostgreSQL CSV** -PostgreSQL逗号分隔文件。
- **PostgreSQL文本** -PostgreSQL文本文件。
- **自定义** -使用用户定义的定界符，转义符和引号字符的文件。

有关写入定界数据的阶段的列表，请参见[按阶段的数据格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-DataFormats/DataFormat_Title.html#concept_jn1_nzb_kv)。