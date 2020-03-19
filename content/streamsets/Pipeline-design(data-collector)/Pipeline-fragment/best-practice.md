# 片段提示和最佳做法

在设计和使用管道片段时，请使用以下提示和最佳实践。

## 设计最佳实践

- 使用可识别的片段名称和描述

  确保管道片段名称和描述提供了足够的信息，以便开发人员可以轻松地从更大的管道片段列表中选择要使用的片段。

- 使用详细的提交消息来区分片段版本

  使用详细的提交消息来帮助数据工程师确定要使用的片段的版本。

- （可选）使用管道标签或标记来编纂片段版本

  当您准备好要进行测试或生产的版本时，可以使用 [管道标签](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Pipelines/PipelineLabels.html#concept_icg_js4_qx)或[管道标签](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Pipelines/PipelineHistory.html#concept_a22_5bp_qx)明确定义片段。

- 仔细选择执行引擎和执行模式

  执行引擎和片段执行模式决定了可以包含在片段中的阶段以及可以使用片段的管道。

  选择执行引擎Data Collector或Data Collector Edge后，您将无法对其进行更改。您可以根据需要配置片段执行模式，但是对其进行更改可能会使片段的最新版本对使用先前版本的所有管道无效。

  有关更多信息，请参见[执行引擎和执行模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Fragments/FragmentConfiguration.html#concept_vsw_m2g_sdb)。

- 选择合适的创作数据收集器

  在配置管道片段时，请选择与要用于运行使用管道片段的管道的Data Collector相同版本的创作数据收集器。使用不同的Data Collector版本可能会导致开发出对执行Data Collector无效的管道片段。

  您必须使用Data Collector版本3.2.0.0或更高版本。

- 评估要使用的输入和输出流的数量

  发布管道片段之前，请仔细考虑该片段使用的输入和输出流的数量。发布管道片段后，将无法在后续片段版本中更改输入或输出流的数量。

  这样可以防止在更新管道使用的片段版本时使管道无效。有关更多信息，请参见[片段输入和输出](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Fragments/FragmentConfiguration.html#concept_mpt_dnd_4db)。

- 使用开发者身份处理器创建其他输入或输出流

  您可以使用Dev Identity处理器在片段中创建其他输入或输出流，在结果片段阶段中创建相应的输入或输出流。有关更多信息，请参见[创建其他流](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Fragments/FragmentConfiguration.html#concept_sbg_vv2_rdb)。

- 使用数据预览测试片段逻辑

  您可以使用数据预览来帮助设计和测试片段的处理逻辑。您可以使用[测试原点](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/TestOrigin.html#concept_sgt_s5v_g2b)为数据预览提供源数据。当使用不包含起点的片段时，这尤其有用。当片段包含原点时，您也可以使用原点来提供预览的源数据。

- 使用管道显式验证来验证片段

  目前，在设计片段时不能使用显式验证。最佳实践是在生产管道中使用片段之前，先在测试管道中验证该片段。有关更多信息，请参见[显式验证](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Fragments/FragmentConfiguration.html#concept_ymr_3sh_qdb)。

- 发布片段的新版本后，查看使用片段的先前版本的管道

  您可以在“片段详细信息”窗格中查看使用每个片段版本的管道。发布片段的新版本后，您可能需要[检查使用较早版本的管道，](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Fragments/FragmentsinPipelines.html#concept_k4y_qmh_qdb)以查看它们是否应使用更新的片段版本。

## 使用技巧

- 选择正确版本的管道片段

  使用“片段提交/标记”阶段属性选择要在管道中使用的片段的版本。有关更多信息，请参见[使用片段版本](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Fragments/FragmentsinPipelines.html#concept_k4y_qmh_qdb)。

- 如果需要，请覆盖片段中定义的运行时参数值

  管道继承了片段中定义的运行时参数。您可以覆盖这些运行时参数的默认值。如果最终从管道中删除了片段，也可以删除该参数。

  有关在管道片段中使用运行时参数的更多信息，请参见“ [运行时参数”](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Fragments/FragmentConfiguration.html#concept_hhv_2jj_qdb)。

- 使用数据预览或显式验证来验证和验证片段处理

  在管道中使用片段时，可以使用管道验证和数据预览来验证片段是否按预期处理数据。