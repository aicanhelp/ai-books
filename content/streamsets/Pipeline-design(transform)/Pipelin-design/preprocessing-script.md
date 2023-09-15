# 预处理脚本

您可以在管道属性中指定Scala预处理脚本，以在管道启动之前执行任务。使用适用于群集上安装的Spark版本的Spark API开发脚本，该版本必须与Scala 2.11.x兼容。在管道中使用预处理脚本之前，请完成[先决条件任务](https://streamsets.com/documentation/controlhub/latest/help/transformer/Installation/StagePrerequisites.html#concept_tnn_2n5_rjb)。

您可能使用预处理脚本来注册要在管道中使用的用户定义函数（UDF）。注册UDF后，可以在允许使用Spark SQL的管道中的任何地方使用它，包括[Spark SQL Expression](https://streamsets.com/documentation/controlhub/latest/help/transformer/Processors/SparkSQLExp.html#concept_akj_gsz_mhb)或[Spark SQL查询](https://streamsets.com/documentation/controlhub/latest/help/transformer/Processors/SparkSQLQuery.html#concept_lnt_kx3_xgb) 处理器，[Join](https://streamsets.com/documentation/controlhub/latest/help/transformer/Processors/Join.html#concept_xdr_slq_sgb)或 [Filter](https://streamsets.com/documentation/controlhub/latest/help/transformer/Processors/Filter.html#concept_fqx_mzb_chb)处理器等。

请注意，SDF未优化UDF，因此应谨慎使用。

例如，以下Scala脚本将整数递增1，并将UDF注册为名为Scala函数`inc`和名为Spark函数 `inc`：

```
def inc(i: Integer): Integer = {
  i + 1
}
spark.udf.register("inc", inc _)
```

当您将此UDF作为预处理脚本在管道中注册后，可以通过`inc _`作为Spark函数调用在管道阶段中调用UDF 。

要为管道指定预处理脚本，请在管道属性面板中单击“高级”选项卡，然后在“预处理脚本”属性中定义脚本。

有关Spark Scala API的更多信息，请参见[Spark文档](https://spark.apache.org/docs/latest/api/scala/index.html#package)。