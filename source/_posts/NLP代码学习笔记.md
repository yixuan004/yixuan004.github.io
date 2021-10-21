---
title: NLP代码学习笔记
date: 2021-10-09 11:52:19
tags:
    - NLP
categories:
	- NLP
---

记录一些代码中常用的操作说明及bug解决记录

<!--more-->

# nn.Embedding.from_pretrained

整个代码的上下文是在做label_ids和slot_ids的embedding
```python
def initialize_slot_value_lookup(self, label_ids, slot_ids):
    '''
    self.sv_encoder = BertForUtteranceEncoding.from_pretrained(
            os.path.join(args.bert_dir, 'bert-base-uncased')
        )
    # 作者把调用sv_encoder的部分fix住
    for p in self.sv_encoder.bert.parameters():
        p.requires_grad = False
    '''
    self.sv_encoder.eval() # 切换到evaluate模式

    # Slot encoding，这个是fix住的部分
    slot_type_ids = torch.zeros(slot_ids.size(), dtype=torch.long).to(self.device) # 初始化一个空的slot_type_ids
    slot_mask = slot_ids > 0
    hid_slot, _ = self.sv_encoder(slot_ids.view(-1, self.max_label_length),
                                    slot_type_ids.view(-1, self.max_label_length),
                                    slot_mask.view(-1, self.max_label_length),
                                    output_all_encoded_layers=False) # 获取CLS的token，hidden的
    hid_slot = hid_slot[:, 0, :] # 博客中指出，这样的操作是在获取CLStoken，而CLStoken是用来进行分类的，也一般被认为是整句话的embedding
    hid_slot = hid_slot.detach()
    self.slot_lookup = nn.Embedding.from_pretrained(hid_slot, freeze=True) # slot的embedding结果，是不可以训练的
```

# 报在同时使用cpu和gpu的错误

报在同时使用cpu和gpu的错误除了tensor要tensor.to(device)以外，model也要.to(device)，否则可能会报在同时使用cpu和gpu的错误

# pytorch中判断两个tensor是否相等

1. tensor.equal()方法
```python
# 该方法用于比较两个tensor是否一样，一样则返回True否则为False
a = torch.tensor([1,2,3,4])
b = torch.tensor([1,2,3,4])
print(a.equal(b))    # 返回True
```

2. tensor.eq()方法
```python
# 该方法用于主元素比较是否相等，相等则在对应位置返回True，否则为False
a = torch.tensor([1,2,2,3])
b = torch.tensor([2,2,3,3])
print(a.eq(b))  # 返回tensor([False,True,False,True]),与a==b返回的结果一样
```

# pytorch输出整个tensor的方法
```python
torch.set_printoptions(profile="full")
print(x) # prints the whole tensor
torch.set_printoptions(profile="default") # reset
print(x) # prints the truncated tensor
```
在这样的输出下，之后就可以写到文件里了

# tensor = tensor[0, :, 0]
这种操作可能代表着仅需要获取bert的cls token的embedding结果，也被认为是整句话的embedding

# .contigous().view()

```python
.contigous().view()
```
有些tensor并不是占用一整块内存，而是由不同的数据块组成，而tensor的view()操作依赖于内存是整块的，这时只需要执行contigous()这个函数，把tensor变成在内存中连续分布的形式，再使用view。

Pytorch0.4中，增加了一个reshape函数，就相当于contigous().view()的功能了！

# pytorch常用的张量操作及归一化算法实现

> https://zhuanlan.zhihu.com/p/76255917

# .squeeze() 和 .unsqueeze()

```python
.squeeze()
.unsqueeze()
```
squeeze()为压缩的意思，即去掉维度数为1的dim，默认是去掉所有为1的，但是也可以自己指定，但如果指定的维度不为1则不会发生任何改变。

unsqueeze(dim)则与squeeze(dim)正好相反，为添加一个维度的作用。

```python
# print("hidden.shape: ", hidden.shape) # torch.Size([96, 1, 768]
hidden = hidden.squeeze() # .squeeze()舍弃维度
# print("after .squeeze(), hidden.shape: ", hidden.shape) # torch.Size([96, 768])
```

# nn.GRU 与 nn.LSTM

循环神经网络是一种能够自适应的变长网络，能够对带有上下文的连续序列很好地进行编码

> https://pytorch.org/docs/stable/torch.html

## 基本文档说明

**参数设置**
```python
input_size: 单个LSTM神经元的输入维度
hidden_size: 单个LSTM神经元的隐含层输出维度
num_layers: LSTM的层数，这里指的是叠起来的层数，而不是展开的层数，展开是自适应的。
bias: 计算过程中是否需要偏置
batch_first: batch是否位于第一个维度，很多时候容易混淆，将在之后进一步解释
dropout: 其中每一层输出的dropout概率，默认为0即不进行dropout，需要注意的一点是最后一层的输出是不会加上dropout概率的。也就是说，当只用到一层LSTM的时候，这个参数是不起作用的。
bidirectional: 是否双向，当设置为True的时候，输出会为将双向LSTM的输出进行拼接，输出的feature size会增加一倍
proj_size: 很多博客中都没有解释，用到的时候可能需要参考 # https://pytorch.org/docs/stable/generated/torch.nn.LSTM.html#torch.nn.LSTM

self.nbt = nn.LSTM(input_size=self.bert_output_dim,
                              hidden_size=self.hidden_dim,
                              num_layers=self.rnn_num_layers,
                              dropout=self.hidden_dropout_prob,
                              batch_first=True)

self.nbt = nn.GRU(input_size=self.bert_output_dim,
                              hidden_size=self.hidden_dim, # args.hidden_dim
                              num_layers=self.rnn_num_layers,
                              dropout=self.hidden_dropout_prob,
                              batch_first=True)
```

**Inputs**
```python
input: input, (h_0, c_0)

input: 当batch_first = False的时候(L, N, H_in)，当batch_first=True的时候(N, L, H_in)
h_0: (D*num_layers, N, H_out)，containing the initial hidden state for each element in the batch. Defaults to zeros if (h_0, c_0) is not provided.
c_0: (D*num_layers, N, H_cell)，containing the initial cell state for each element in the batch. Defaults to zeros if (h_0, c_0) is not provided.

其中：
    N是batch_size
    L是每句话的长度
    如果使用双向LSTM则D是2，否则是1
    H_in是输入的hiddendim（例如是bert的输出768）
    H_cell是LSTM内部的hidden_size
    H_out和输入参数中的proj_size相关，但基本可以理解为就是hidden_size，
```

**Outputs**
```python
Outputs: output, (h_n, c_n)

output: 当batch_first=False的时候是(L, N, D*H_out)，当batch_first=True的时候是(N, L, D*H_out)，其中包括了LSTM最后一层的输出h_t，对于每个t时刻。在PackedSequence相关上还有其他的操作，不过暂时就先不管了
h_n: (D*num_layers, N, H_out)包含了每个batch中最后的一个hidden state的element
c_n: (D*num_layersm N, H_cell)包含了最后一个cell的state，对于每个batch的最后一个element？
```

## 关于循环神经网络

### 关于输入输出的三个维度

自：维度在tensor的变化中始终是最关键的部分，怎么理解维度背后的含义？

对于输入输出，我们首先需要注意是传给的网络输出必须是三维的
其中每个维度代表的意思，我们习惯的方式是[batch_size, sequence_length, feature_size]
具体来说，假如输入的是句子的话，每个维度的含义就是：

[一次投入到网络中的句子的条数，句子的长度，句子中每个单词对应的向量维度]

自：在SUMBT代码中，这里的输入该怎么一步步的理解

### 关于batch first

这个是一个非常有趣的参数，他能够将输入的形式变为我们习惯的[batch_size, seq_len, feature_size]

也就是说原本输入参数的形式是[seq_len, batch_size, feature_size]可以视作原本为一列一句话，现在给我们改成了更习惯的一行一句话

更通俗的来说，就是原本一行为一个句子，变成每一列为一个句子，其实设置了batch_first，也不过是在内部也是使用了第1维度和第2维度的转置操作来变成初始形式

在SUMBT中怎么理解这个事情？......

# DST任务中的slot_accuracy和joint_accuracy

slot_accuracy:
```python
acc_slot = torch.sum(accuracy, 0).float() / torch.sum(labels.view(-1, slot_dim) > -1, 0).float()
```
个人总结：按照三个槽分别算，对的除以总的（需要去除padding）就是slot_accuracy


joint_accuracy:
```python
acc = sum(torch.sum(accuracy, 1) / slot_dim).float() / torch.sum(labels[:, :, 0].view(-1) > -1, 0).float() # joint accuracy
```
个人总结：每轮对话的算成一个，例如在每轮对话中有3个槽，对了2个，该轮对话就是0.66，之后把所有轮对话的加在一起，除以对话的有效轮数就是joint_accuracy

# tqdm中的desc参数

这里desc参数是进度条的前缀名称
```python
tqdm(dev_dataloader, desc="Validation")
```

# tensorboard的使用

看起来tensorboard和tensorboardX不是一个东西？ 所以需要使用pip install进行安装(venv环境下)
```shell
$ pip install tensorboard
...
(venvsumbt) lyx@h1:/hdd1/lyx$ tensorboard
TensorFlow installation not found - running with reduced feature set.
Error: A logdir or db must be specified. For example `tensorboard --logdir mylogdir` or `tensorboard --db sqlite:~/.tensorboard.db`. Run `tensorboard --helpfull` for details and examples.
```

使用方法如下（SUMBT-lyx为例）：
```shell
(venvsumbt) lyx@h1:/hdd1/lyx/SUMBT-lyx$ tensorboard --logdir='SUMBT-lyx/tensorboard/output'
TensorFlow installation not found - running with reduced feature set.

NOTE: Using experimental fast data loading logic. To disable, pass
    "--load_fast=false" and report issues on GitHub. More details:
    https://github.com/tensorflow/tensorboard/issues/4784

Serving TensorBoard on localhost; to expose to the network, use a proxy or pass --bind_all
TensorBoard 2.7.0 at http://localhost:6007/ (Press CTRL+C to quit)
```

此时还需要配合一条端口转发命令：
```shell
ssh lyx@xxx.xxx.xxx.xxx -L 6007:localhost:6007
```

注意要在训练前另开一个bash执行如下，然后再开启训练，否则可能会出现tensorboard没有显示的情况
使用绝对路径！
```shell
tensorboard --logdir=/hdd1/lyx/SUMBT-lyx/tensorboard/ckpt-output

tensorboard --logdir=/hdd1/lyx/SUMBT-lyx/tensorboard/20211020-1152-lyx测试
```

# TensorDataset，SequentialSampler，Dataloader相关

Reference:
> 
> https://pytorch.org/docs/stable/data.html?highlight=sequentialsampler#torch.utils.data.SequentialSampler


在代码中看到：
```python
xxx_sampler = SequentialSampler(xxx_data)  or RandomSampler(xxx_data)
xxx_dataloader = DataLoader(xxx_data, sampler=xxx_sampler, batch_size=...)
```

自：一般来说在训练过程中使用RandomSampler，dev和test过程中使用SequentialSampler，

在DST任务中因为和上下文一些状态有关，所以是不是只能顺序采样

## CLASS torch.utils.data.SequentialSampler(data_source)
按顺序采样元素，始终按相同顺序采样（构建一个迭代器）

源代码是：
```python
class SequentialSampler(Sampler[int]):
    r"""Samples elements sequentially, always in the same order.

    Args:
        data_source (Dataset): dataset to sample from
    """
    data_source: Sized

    def __init__(self, data_source: Sized) -> None:
        self.data_source = data_source

    def __iter__(self) -> Iterator[int]:
        return iter(range(len(self.data_source)))

    def __len__(self) -> int:
        return len(self.data_source)
```

## CLASS torch.utils.data.RandomSampler(data_source, replacement=False, num_samples=None, generator=None)
随机抽取元素样本。如果没有替换，则从无序数据集中采样。如果使用替换，则用户可以指定要绘制的样本数

源代码见：
> https://pytorch.org/docs/stable/_modules/torch/utils/data/sampler.html#RandomSampler