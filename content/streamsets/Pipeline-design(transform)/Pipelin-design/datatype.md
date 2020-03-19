# 资料类型

变压器使用Spark数据类型处理数据。

某些数据类型在特定情况下可能无效。例如，基于文本的数据格式（例如Delimited，JSON和Text）不支持处理二进制数据。同样，“定界”和“文本”数据格式不支持处理复杂类型，例如列表或地图。

要将数据从管道中的一种类型更改为另一种类型，可以使用[类型转换器处理器](https://streamsets.com/documentation/controlhub/latest/help/transformer/Processors/Fields-Referencing.html#concept_ygc_5hy_zgb)。

有关Spark数据类型的详细信息，请参见[Spark文档](https://spark.apache.org/docs/latest/sql-reference.html)。

## 预览中的数据类型

当你[预览数据](https://streamsets.com/documentation/controlhub/latest/help/transformer/Preview/Preview-Overview.html#concept_cgw_lkb_ghb)，预览面板显示器通用数据类型，如布尔值，字符串和列表。这些数据类型表示正在使用的Spark数据类型。

例如，在预览中，List表示Array Spark数据类型，而Map可以表示Map或Struct Spark数据类型。