# 快速开始

## 哲学

Transformers是为寻求使用/研究/扩展大型变压器模型的NLP研究人员而建的自有图书馆。

该库在设计时考虑了两个强烈的目标：

- 尽可能容易且快速地使用：
  - 我们严格限制了要学习的面向用户抽象的数量，实际上几乎没有抽象，使用每个模型只需要三个标准类：配置，模型和令牌生成器，
  - 所有这些类都可以使用通用的`from_pretrained()`实例化方法，以简单统一的方式从预训练的实例中初始化，该方法将负责从库或您自己提供的预训练实例中下载（如果需要），缓存和加载相关类。保存的实例。
  - 因此，该库不是神经网络构建模块的模块化工具箱。如果要扩展/构建该库，只需使用常规的Python / PyTorch模块并从该库的基类继承即可重用模型加载/保存之类的功能。
- 提供性能与原始模型尽可能接近的最新模型：
  - 对于每种架构，我们至少提供一个示例，该示例再现了该架构的正式作者提供的结果，
  - 该代码通常尽可能地接近原始代码库，这意味着某些PyTorch代码可能不像转化为TensorFlow代码那样具有*自爆性*。

其他一些目标：

- 尽可能一致地公开模型的内部：
  - 我们使用单个API授予访问权限的全部隐藏状态和关注权重，
  - 标记器和基本模型的API已标准化，可以在模型之间轻松切换。
- 纳入主观选择的有前途的工具，以对这些模型进行微调/研究：
  - 一种简单/一致的方法，可以向词汇表和嵌入物中添加新标记以进行微调，
  - 遮盖和修剪变压器头的简单方法。

## 主要概念

该库针对每种模型围绕三种类型的类构建：

- **模型类**是`torch.nn.Modules`库中当前提供的8种模型架构的PyTorch模型（），例如`BertModel`
- **配置类**，用于存储构建模型所需的所有参数，例如`BertConfig`。您不必总是实例化这些自身，尤其是如果您使用的是经过预训练的模型而未进行任何修改，则创建模型将自动照顾实例化配置（这是模型的一部分）
- **标记程序类**，用于存储每个模型的词汇表，并提供用于编码/解码要嵌入模型的标记嵌入索引列表中的字符串的方法，例如`BertTokenizer`

所有这些类都可以从经过预训练的实例中实例化，并使用两种方法在本地保存：

- `from_pretrained()`让你实例/配置/标记者从库本身可能提供了预训练版本（目前27款是作为上市模式[在这里](https://huggingface.co/transformers/pretrained_models.html)）由用户或存储在本地（或服务器上），
- `save_pretrained()`使您可以在本地保存模型/配置/令牌，以便可以使用来重新加载它`from_pretrained()`。

我们将通过一些简单的快速入门示例来结束本快速入门之旅，以了解如何实例化和使用这些类。本文档的其余部分分为两部分：

- “ **主要类别”**部分详细介绍了三种主要类别（配置，模型，令牌生成器）的常见功能/方法/属性，以及一些作为培训实用程序提供的与优化相关的类别，
- 该**装基准**部分的所有细节每个班级每个模型架构的变体，特别是呼吁他们每个人在输入/输出，你应该期望。

## 快速导览：用法

这是两个示例，展示了一些`Bert`和`GPT2`类以及预训练的模型。

有关每个模型类的示例，请参见完整的API参考。

### BERT示例

让我们从使用文本字符串准备标记化的输入（将要馈送到Bert的标记嵌入索引的列表）开始 `BertTokenizer`

```python
import torch
from transformers import BertTokenizer, BertModel, BertForMaskedLM

# OPTIONAL: if you want to have more information on what's happening under the hood, activate the logger as follows
import logging
logging.basicConfig(level=logging.INFO)

# Load pre-trained model tokenizer (vocabulary)
tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')

# Tokenize input
text = "[CLS] Who was Jim Henson ? [SEP] Jim Henson was a puppeteer [SEP]"
tokenized_text = tokenizer.tokenize(text)

# Mask a token that we will try to predict back with `BertForMaskedLM`
masked_index = 8
tokenized_text[masked_index] = '[MASK]'
assert tokenized_text == ['[CLS]', 'who', 'was', 'jim', 'henson', '?', '[SEP]', 'jim', '[MASK]', 'was', 'a', 'puppet', '##eer', '[SEP]']

# Convert token to vocabulary indices
indexed_tokens = tokenizer.convert_tokens_to_ids(tokenized_text)
# Define sentence A and B indices associated to 1st and 2nd sentences (see paper)
segments_ids = [0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 1, 1]

# Convert inputs to PyTorch tensors
tokens_tensor = torch.tensor([indexed_tokens])
segments_tensors = torch.tensor([segments_ids])
```

让我们看看如何使用`BertModel`隐藏状态对输入进行编码：

```python
# Load pre-trained model (weights)
model = BertModel.from_pretrained('bert-base-uncased')

# Set the model in evaluation mode to deactivate the DropOut modules
# This is IMPORTANT to have reproducible results during evaluation!
model.eval()

# If you have a GPU, put everything on cuda
tokens_tensor = tokens_tensor.to('cuda')
segments_tensors = segments_tensors.to('cuda')
model.to('cuda')

# Predict hidden states features for each layer
with torch.no_grad():
    # See the models docstrings for the detail of the inputs
    outputs = model(tokens_tensor, token_type_ids=segments_tensors)
    # Transformers models always output tuples.
    # See the models docstrings for the detail of all the outputs
    # In our case, the first element is the hidden state of the last layer of the Bert model
    encoded_layers = outputs[0]
# We have encoded our input sequence in a FloatTensor of shape (batch size, sequence length, model hidden dimension)
assert tuple(encoded_layers.shape) == (1, len(indexed_tokens), model.config.hidden_size)
```

以及如何用于`BertForMaskedLM`预测屏蔽令牌：

```python
# Load pre-trained model (weights)
model = BertForMaskedLM.from_pretrained('bert-base-uncased')
model.eval()

# If you have a GPU, put everything on cuda
tokens_tensor = tokens_tensor.to('cuda')
segments_tensors = segments_tensors.to('cuda')
model.to('cuda')

# Predict all tokens
with torch.no_grad():
    outputs = model(tokens_tensor, token_type_ids=segments_tensors)
    predictions = outputs[0]

# confirm we were able to predict 'henson'
predicted_index = torch.argmax(predictions[0, masked_index]).item()
predicted_token = tokenizer.convert_ids_to_tokens([predicted_index])[0]
assert predicted_token == 'henson'
```

### OpenAI GPT-2

这是一个快速入门示例，该示例使用`GPT2Tokenizer`和`GPT2LMHeadModel`类以及OpenAI的预训练模型来预测文本提示中的下一个标记。

首先，我们使用以下命令从文本字符串准备标记化的输入 `GPT2Tokenizer`

```python
import torch
from transformers import GPT2Tokenizer, GPT2LMHeadModel

# OPTIONAL: if you want to have more information on what's happening, activate the logger as follows
import logging
logging.basicConfig(level=logging.INFO)

# Load pre-trained model tokenizer (vocabulary)
tokenizer = GPT2Tokenizer.from_pretrained('gpt2')

# Encode a text inputs
text = "Who was Jim Henson ? Jim Henson was a"
indexed_tokens = tokenizer.encode(text)

# Convert indexed tokens in a PyTorch tensor
tokens_tensor = torch.tensor([indexed_tokens])
```

让我们看看如何使用它`GPT2LMHeadModel`来在文本之后生成下一个标记：

```python
# Load pre-trained model (weights)
model = GPT2LMHeadModel.from_pretrained('gpt2')

# Set the model in evaluation mode to deactivate the DropOut modules
# This is IMPORTANT to have reproducible results during evaluation!
model.eval()

# If you have a GPU, put everything on cuda
tokens_tensor = tokens_tensor.to('cuda')
model.to('cuda')

# Predict all tokens
with torch.no_grad():
    outputs = model(tokens_tensor)
    predictions = outputs[0]

# get the predicted next sub-word (in our case, the word 'man')
predicted_index = torch.argmax(predictions[0, -1, :]).item()
predicted_text = tokenizer.decode(indexed_tokens + [predicted_index])
assert predicted_text == 'Who was Jim Henson? Jim Henson was a man'
```

可以在[文档中](http://localhost:8889/lab#documentation)找到每种模型架构（Bert，GPT，GPT-2，Transformer-XL，XLNet和XLM）的每个模型类的示例。

#### 使用过去

GPT-2以及其他一些模型（GPT，XLNet，Transfo-XL，CTRL）都使用`past`或`mems`属性，当使用顺序解码时，可以使用或属性来防止重新计算键/值对。当生成序列作为注意力机制的重要组成部分得益于先前的计算时，它很有用。

这是一个使用`past`with `GPT2LMHeadModel`和argmax解码的完整示例（仅应作为示例，因为argmax解码会带来很多重复）：

```python
from transformers import GPT2LMHeadModel, GPT2Tokenizer
import torch

tokenizer = GPT2Tokenizer.from_pretrained("gpt2")
model = GPT2LMHeadModel.from_pretrained('gpt2')

generated = tokenizer.encode("The Manhattan bridge")
context = torch.tensor([generated])
past = None

for i in range(100):
    print(i)
    output, past = model(context, past=past)
    token = torch.argmax(output[0, :])

    generated += [token.tolist()]
    context = token.unsqueeze(0)

sequence = tokenizer.decode(generated)

print(sequence)
```

该模型仅需要单个令牌作为输入，因为所有先前令牌的键/值对都包含在中`past`。

### Model2Model示例

编码器-解码器体系结构需要两个标记化的输入：一个用于编码器，另一个用于解码器。假设我们要`Model2Model`用于生成式问题解答，并首先标记化将被馈送到模型的问题和答案。

```python
import torch
from transformers import BertTokenizer, Model2Model

# OPTIONAL: if you want to have more information on what's happening under the hood, activate the logger as follows
import logging
logging.basicConfig(level=logging.INFO)

# Load pre-trained model tokenizer (vocabulary)
tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')

# Encode the input to the encoder (the question)
question = "Who was Jim Henson?"
encoded_question = tokenizer.encode(question)

# Encode the input to the decoder (the answer)
answer = "Jim Henson was a puppeteer"
encoded_answer = tokenizer.encode(answer)

# Convert inputs to PyTorch tensors
question_tensor = torch.tensor([encoded_question])
answer_tensor = torch.tensor([encoded_answer])
```

让我们看看如何使用它`Model2Model`来获得与此（问题，答案）对相关的损失值：

```python
# In order to compute the loss we need to provide language model
# labels (the token ids that the model should have produced) to
# the decoder.
lm_labels =  encoded_answer
labels_tensor = torch.tensor([lm_labels])

# Load pre-trained model (weights)
model = Model2Model.from_pretrained('bert-base-uncased')

# Set the model in evaluation mode to deactivate the DropOut modules
# This is IMPORTANT to have reproducible results during evaluation!
model.eval()

# If you have a GPU, put everything on cuda
question_tensor = question_tensor.to('cuda')
answer_tensor = answer_tensor.to('cuda')
labels_tensor = labels_tensor.to('cuda')
model.to('cuda')

# Predict hidden states features for each layer
with torch.no_grad():
    # See the models docstrings for the detail of the inputs
    outputs = model(question_tensor, answer_tensor, decoder_lm_labels=labels_tensor)
    # Transformers models always output tuples.
    # See the models docstrings for the detail of all the outputs
    # In our case, the first element is the value of the LM loss 
    lm_loss = outputs[0]
```

这种损失可用于微调`Model2Model`答疑任务。假设我们对模型进行了微调，现在让我们看看如何生成答案：

```python
# Let's re-use the previous question
question = "Who was Jim Henson?"
encoded_question = tokenizer.encode(question)
question_tensor = torch.tensor([encoded_question])

# This time we try to generate the answer, so we start with an empty sequence
answer = "[CLS]"
encoded_answer = tokenizer.encode(answer, add_special_tokens=False)
answer_tensor = torch.tensor([encoded_answer])

# Load pre-trained model (weights)
model = Model2Model.from_pretrained('fine-tuned-weights')
model.eval()

# If you have a GPU, put everything on cuda
question_tensor = encoded_question.to('cuda')
answer_tensor = encoded_answer.to('cuda')
model.to('cuda')

# Predict all tokens
with torch.no_grad():
    outputs = model(question_tensor, answer_tensor)
    predictions = outputs[0]

# confirm we were able to predict 'jim'
predicted_index = torch.argmax(predictions[0, -1]).item()
predicted_token = tokenizer.convert_ids_to_tokens([predicted_index])[0]
assert predicted_token == 'jim'
```