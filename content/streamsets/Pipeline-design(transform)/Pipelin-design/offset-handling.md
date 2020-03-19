# 胶印处理

变形金刚使用偏移量来跟踪处理进度。变压器跟踪大多数原点和代理密钥生成器处理器的偏移量：

- 原点偏移

  原点偏移使Transformer能够跟踪已处理的数据，从而可以跟踪应该继续处理的数据。

  在流传输管道中，每次读取，处理和写入一批数据时，Transformer都会通过存储偏移来跟踪已处理的数据。在Transformer从目标系统收到确认已将批处理写入系统后，将保存偏移。

  在批处理管道中，Transformer无需在管道运行时存储偏移量，因为Transformer会在单个批处理中处理所有可用数据，然后停止管道。

  默认情况下，两种管道的管道运行之间都存储偏移。当具有存储偏移量的原点的管道正常停止时，Transformer会默认存储运行管道的偏移量。这使Transformer可以在下次启动管道时从中断处开始处理。平稳停止是Transformer在停止之前执行所有预期任务的停止。这不包括强制停止或类似的关闭情况下变压器前机变压器停止。

  您可以配置任何跟踪偏移量的原点以[跳过跟踪偏移量](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Offsets.html#concept_qqc_xsx_gjb)。另外，您可以在启动[管道](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Offsets.html#concept_ygg_ryx_gjb)时[重置所有管道偏移](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Offsets.html#concept_ygg_ryx_gjb)，以使Transformer像第一次一样运行管道。

  Transformer 维护可包含在批处理和流传输管道中的所有原点的偏移量。Transformer不会为只能包含在批处理管道中的以下原点保留偏移量：三角洲湖库杜整个目录

  由于这些原点不跟踪偏移量，因此每次管道运行时它们都会读取所有可用数据。

- 处理器偏移

  管道运行时，Transformer跟踪代理密钥生成器处理器的偏移量，以确保处理器不会生成重复的密钥。

  默认情况下，Transformer保存流水线运行之间的偏移，以便在重新启动流水线时，代理密钥生成器处理器将继续生成大于上次保存的偏移的密钥。

  如果在[启动管道](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Offsets.html#concept_ygg_ryx_gjb)时[重置管道偏移](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Offsets.html#concept_ygg_ryx_gjb)，则代理密钥生成器处理器的偏移也会重置。结果，处理器以指定的初始值开始密钥生成。

## 跳过偏移跟踪

您可以配置任何跟踪偏移的原点以跳过跟踪偏移。您不能将代理密钥生成器处理器配置为跳过跟踪偏移。

当您希望原点将每批处理都像管道刚开始运行时一样跳过偏移跟踪。在某些情况下这可能是适当的。

例如，假设您希望管道在每次运行管道时都处理Hive表中的所有数据。为了获得理想的结果，您可以在批处理管道中使用Hive原点来读取单个批处理中的所有数据。然后，在原点中启用“跳过偏移跟踪”属性，以确保每次运行管道时都处理所有数据。如果允许偏移量跟踪，则管道将读取第一个管道运行中的所有可用数据，但在后续运行中，它将仅读取自上次管道运行以来到达的数据。

在[缓慢变化的尺寸](https://streamsets.com/documentation/controlhub/latest/help/transformer/Processors/SCDimension.html#concept_rnp_nxr_j3b)流传输管道中，跳过偏移跟踪至关重要，在该管道中您想将更改数据与最新的主尺寸数据进行比较。在这种情况下，您将跳过主原点中的偏移跟踪，因此，每当管道从更改原点处理数据时，主原点都会读取主尺寸数据。这允许“缓慢更改尺寸”处理器将更改与主尺寸数据进行比较。如果不跳过偏移量跟踪，则主原点将仅读取新的主尺寸数据，从而提供不完整的主数据集以进行比较。

跳过偏移跟踪也可能是完全不合适的，因此您应谨慎跳过偏移跟踪。

请注意，大多数流传输管道都需要偏移量跟踪才能按预期运行。例如，您通常希望Kafka原点从指定的初始偏移量读取消息，以处理该点以后的所有现有消息，并继续处理新到达的消息。如果您跳过偏移量跟踪，则原点会从每批批次的初始偏移量开始重新处理数据。

要跳过跟踪偏移，请在原点的“常规”选项卡上，选择“跳过偏移跟踪”属性。如果原点不具有该属性，则它不会跟踪偏移量。

## 重置管道偏移

您可以选择在启动管道之前重置所有管道偏移。重置管道偏移时，Transformer就像第一次运行管道一样运行管道。

例如，假设您有一个每周运行的批处理管道。它包括一个ADLS Gen2源，该源从/ logs目录读取文件。管道处理完所有可用数据后，原点会记录偏移量-在这种情况下，是最后处理文件的最后修改时间戳。然后，管道停止。下次运行管道时，管道仅处理在该偏移量之后具有最后修改时间戳的文件。

现在，假设您需要更改管道写入的目标系统，并且想要重新处理所有可用数据以将结果写入新的目标系统。为此，您替换管道中的目标。然后，当您启动管道时，可以使用“重置偏移量和开始”选项。

管道将在一个批次中处理所有可用数据并停止。和以前一样，它存储偏移量。然后，在后续的管道运行中，它将从最后保存的偏移量继续进行处理。

要在启动管道之前重置管道偏移，请单击“开始”按钮（![img](imgs/icon-StartMenu.png)）右侧的菜单箭头，然后单击“ **重置偏移和开始”**。