## FunctionDef get_keyword_embedding(bert_model, tokenizer, key_words)
**get_keyword_embedding**: 该函数的功能是获取关键词的嵌入表示。

**参数**:
- **bert_model**: 一个预训练的BERT模型，用于生成嵌入表示。
- **tokenizer**: 与BERT模型相匹配的分词器，用于将关键词转换为模型能理解的格式。
- **key_words**: 一个字符串列表，包含需要获取嵌入表示的关键词。

**代码描述**:
`get_keyword_embedding`函数首先使用传入的`tokenizer`将关键词列表`key_words`转换为模型能够处理的输入格式。这一步骤包括将关键词转换为对应的输入ID，并对输入进行填充和截断以满足模型的要求。随后，函数从`tokenizer`的输出中提取`input_ids`，并去除每个序列的首尾特殊标记，因为这些标记对于关键词的嵌入表示不是必需的。

接着，函数利用`bert_model`的`embeddings.word_embeddings`属性，根据`input_ids`获取对应的嵌入表示。由于可能传入多个关键词，函数对所有关键词的嵌入表示进行平均，以获得一个统一的表示形式。

在项目中，`get_keyword_embedding`函数被`add_keyword_to_model`函数调用，用于将自定义关键词的嵌入表示添加到预训练的BERT模型中。这一过程涉及读取关键词文件，生成关键词的嵌入表示，扩展模型的嵌入层以包含这些新的关键词，最后将修改后的模型保存到指定路径。这使得模型能够理解并有效处理这些新增的关键词，从而提高模型在特定任务上的性能。

**注意**:
- 确保传入的`bert_model`和`tokenizer`是匹配的，即它们来源于同一个预训练模型。
- 关键词列表`key_words`应该是经过精心挑选的，因为这些关键词将直接影响模型的理解能力和性能。
- 在调用此函数之前，应该已经准备好关键词文件，并确保其格式正确。

**输出示例**:
假设传入了两个关键词`["AI", "机器学习"]`，函数可能返回一个形状为`(2, embedding_size)`的张量，其中`embedding_size`是模型嵌入层的维度，表示这两个关键词的平均嵌入表示。
## FunctionDef add_keyword_to_model(model_name, keyword_file, output_model_path)
**add_keyword_to_model**: 该函数的功能是将自定义关键词添加到预训练的嵌入模型中。

**参数**:
- **model_name**: 字符串类型，默认为`EMBEDDING_MODEL`。指定要使用的预训练嵌入模型的名称。
- **keyword_file**: 字符串类型，默认为空字符串。指定包含自定义关键词的文件路径。
- **output_model_path**: 字符串类型，可为`None`。指定添加了关键词后的模型保存路径。

**代码描述**:
首先，函数通过读取`keyword_file`文件，将文件中的每一行作为一个关键词添加到`key_words`列表中。接着，使用指定的`model_name`加载一个句子转换器模型（SentenceTransformer模型），并从中提取第一个模块作为词嵌入模型。通过这个词嵌入模型，可以获取到BERT模型及其分词器。

然后，函数调用`get_keyword_embedding`函数，传入BERT模型、分词器和关键词列表，以获取这些关键词的嵌入表示。接下来，函数将这些新的关键词嵌入添加到BERT模型的嵌入层中。这一步骤包括扩展分词器以包含新的关键词，调整BERT模型的嵌入层大小以适应新增的关键词，并将关键词的嵌入表示直接赋值给模型的嵌入层权重。

最后，如果提供了`output_model_path`参数，则函数会在该路径下创建必要的目录，并将更新后的词嵌入模型以及BERT模型保存到指定位置。这一过程确保了模型能够在后续的使用中，理解并有效处理这些新增的关键词。

**注意**:
- 确保`keyword_file`文件存在且格式正确，每行应包含一个关键词。
- 由于模型的嵌入层大小会根据新增关键词进行调整，因此在添加关键词后，模型的大小可能会增加。
- 在保存模型时，会使用`safetensors`格式保存BERT模型，确保模型的兼容性和安全性。
- 添加关键词到模型是一个影响模型性能的操作，因此应谨慎选择关键词，并考虑到这些关键词在特定任务上的实际应用价值。
## FunctionDef add_keyword_to_embedding_model(path)
**add_keyword_to_embedding_model**: 该函数的功能是将自定义关键词添加到指定的嵌入模型中。

**参数**:
- **path**: 字符串类型，默认为`EMBEDDING_KEYWORD_FILE`。指定包含自定义关键词的文件路径。

**代码描述**:
此函数首先通过`os.path.join(path)`获取关键词文件的完整路径。然后，它从配置中读取模型的名称和路径，这些配置通过`MODEL_PATH["embed_model"][EMBEDDING_MODEL]`获得。接着，函数计算模型所在的父目录，并生成一个包含当前时间戳的新模型名称，格式为`EMBEDDING_MODEL_Merge_Keywords_当前时间`，以确保输出模型名称的唯一性。

接下来，函数调用`add_keyword_to_model`，这是一个重要的调用关系，因为`add_keyword_to_model`负责实际将关键词添加到嵌入模型中。在调用`add_keyword_to_model`时，传入当前使用的模型名称、关键词文件路径以及新模型的保存路径。这一步骤完成了将自定义关键词集成到预训练嵌入模型中的核心功能。

**注意**:
- 确保传入的`path`参数指向一个有效的关键词文件，且该文件格式正确，每行包含一个要添加的关键词。
- 该函数依赖于`add_keyword_to_model`函数，后者负责实际的关键词添加逻辑，包括读取关键词、更新模型的嵌入层以及保存更新后的模型。因此，了解`add_keyword_to_model`的具体实现对于理解整个关键词添加过程是非常重要的。
- 生成的新模型名称包含时间戳，这有助于区分不同时间点生成的模型版本。
- 在使用此函数时，应考虑到模型大小可能会因为添加新的关键词而增加，这可能会对模型加载和运行时的性能产生影响。
