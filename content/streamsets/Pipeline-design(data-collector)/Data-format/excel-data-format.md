# Excel数据格式

您可以使用基于文件的来源，例如[Directory](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Directory.html#concept_qcq_54n_jq)和 [Google Cloud Storage](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/GCS.html#concept_iyd_wql_nbb)来处理Microsoft Excel `.xls`或`.xlsx`文件。

处理Excel文件时，原点会为文件中的每一行创建一条记录。配置原点时，可以指定文件是否包含标题行以及是否忽略标题行。标题行必须是文件的第一行。 无法识别垂直标题列。

Origins无法处理大量行的Excel文件。当原始无法处理Excel数据格式的文件时，可以在Excel中将文件另存为CSV文件，然后使用定界数据格式来处理文件。

对于支持这种数据格式起源的完整列表，请参阅[起源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-DataFormats/DataFormat_Title.html#concept_kgd_11c_kv) “数据格式的舞台”附录。