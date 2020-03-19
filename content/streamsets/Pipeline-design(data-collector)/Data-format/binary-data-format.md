# 二进制数据格式

读取二进制数据时，原点会生成一条记录，在记录的根部有一个单字节数组字段。

当数据超过用户定义的最大数据大小时，原点将无法处理数据。因为未创建记录，所以源无法将记录传递到管道以将其写为错误记录。相反，原点会产生阶段误差。

在写入二进制数据时，目标和处理器将二进制数据写入记录中的单个字段。

有关处理二进制数据的阶段的列表，请参见[按阶段的数据格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-DataFormats/DataFormat_Title.html#concept_jn1_nzb_kv)。