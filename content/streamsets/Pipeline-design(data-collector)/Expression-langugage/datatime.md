# 日期时间变量

表达式语言提供了在表达式中使用的日期时间变量。

您可以使用datetime变量来配置Hadoop FS，Local FS或MapR FS目标，以将记录写入基于时间的目录。

您可以使用datetime变量配置Amazon S3或Elasticsearch目标，以将记录写入基于时间的分区前缀或基于时间的索引。您可以在Hive元数据处理器的分区值表达式中使用它们。

表达式语言提供以下日期时间变量：

- $ {YYYY（）}-四位数的年份
- $ {YY（）}-两位数年份
- $ {MM（）}-两位数月份
- $ {DD（）}-两位日期
- $ {hh（）}-两位小时
- $ {mm（）}-两位数分钟
- $ {ss（）}-两位秒

在大多数阶段中使用日期时间变量时，请使用年份变量之一和要使用的最小变量之间的所有日期时间变量。例如，要每天为Hadoop FS目标创建目录，请使用year变量，month变量和day变量。您可以使用以下日期时间变量级数之一：

```
${YYYY()}-${MM()}-${DD()}
${YY()}_${MM()}_${DD()}
```

在Hive元数据处理器中，您可以根据需要使用datetime变量。