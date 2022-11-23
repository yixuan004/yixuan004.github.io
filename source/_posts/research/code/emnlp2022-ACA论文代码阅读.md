---
title: emnlp2022-ACA论文代码阅读
date: 2022-11-23 09:31:41
tags:
    - code-reading
categories:
	- research
	- code-reading
---

emnlp2022-ACA论文代码阅读

<!--more-->

# emnlp2022-ACA论文代码阅读

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221123094216806.png)

## 1. 入口引起的全局流程

以tacred数据集为例，首先，执行的脚本都在`/bash/tacred/`这个路径下，其中带`_aca`后缀的就代表使用了作者的方法，然后前缀的`emar/re_cre`代表作者这种model-agnostic方法使用的baseline；

以`emar_aca.sh`为例，在单卡GPU上运行，执行的是main/emar.py这个文件

```shell
export PYTHONPATH=`pwd`

CUDA_VISIBLE_DEVICES=$1 python3 main/emar.py \
  --memory_size 10 \
  --total_round 5 \
  --task_name TACRED \
  --data_file data/data_with_marker_tacred.json \
  --relation_file data/id2rel_tacred.json \
  --num_of_train 420 \
  --num_of_val 140 \
  --num_of_test 140 \
  --batch_size_per_step 32 \
  --exp_name EMAR \
  --num_of_relation 40  \
  --cache_file data/TACRED_data.pt \
  --rel_per_task 4 \
  --aca 1 
```

这里详解一下各个参数的意思

### 1.1 main/emar.py

从`if __name__ == '__main__'`开始看，首先使用`get_config()`函数，主要就是读取shell文件中配置的各个超参数

```python
if __name__ == '__main__':
    config = get_config()

import argparse
def get_config():
    parser = argparse.ArgumentParser()
    parser.add_argument('--log_dir', default='./logs', type=str)
    parser.add_argument('--exp_name', default=None, type=str)
    parser.add_argument('--total_round', default=5, type=int)
    parser.add_argument('--seed', default=2021, type=int)
    parser.add_argument('--num_of_relation', default=80, type=int)
    parser.add_argument('--max_length', default=256, type=int)
    parser.add_argument('--rel_per_task', default=4, type=int)
    parser.add_argument('--num_of_train', default=420, type=int)
    parser.add_argument('--num_of_val', default=140, type=int)
    parser.add_argument('--num_of_test', default=140, type=int)
    parser.add_argument('--task_name', default='TACRED', type=str)
    parser.add_argument('--data_file', default='./data/data_with_marker_tacred.json', type=str)
    parser.add_argument('--cache_file', default='./data/TACRED_data.pt', type=str)
    parser.add_argument('--relation_file', default='./data/id2rel_tacred.json', type=str)
    parser.add_argument('--model_name', default='FEA', type=str)
    parser.add_argument('--bert_path', default='bert-base-uncased', type=str)
    parser.add_argument('--max_grad_norm', default=10, type=int)
    parser.add_argument('--device', default='cuda', type=str)
    parser.add_argument('--vocab_size', default=30522, type=int)
    parser.add_argument('--memory_size', default=10, type=int)
    parser.add_argument('--step1_epochs', default=10, type=int)
    parser.add_argument('--step2_epochs', default=10, type=int)
    parser.add_argument('--step3_epochs', default=10, type=int)
    parser.add_argument('--batch_size_per_step', default=32, type=int)
    parser.add_argument('--drop_out', default=0.5, type=float)
    parser.add_argument('--pattern', default='entity_marker', type=str)
    parser.add_argument('--key_size', default=256, type=int)
    parser.add_argument('--head_size', default=768, type=int)
    parser.add_argument('--encoder_output_size', default=768, type=int)
    parser.add_argument('--mem_size', default=768, type=int)
    parser.add_argument('--aca', default=0, type=int)  # 是否加入作者的ACA模块 
    parser.add_argument("--optim", default='adam', type=str)

    config = parser.parse_args()
    return config
```

因为是EMAR.py这个文件，那就首先拼接`config.exp_name`，这个之后将作为logs文件夹中记录的log

```shell
config.exp_name = f'EMAR'
if config.aca:
    config.exp_name += '-aca'
config.exp_name += f'-{config.task_name}-M_{config.memory_size}'
```

如果不存在log_dir那个路径，则mkdir，这里logs是一个config中的默认路径`./logs`

```shell
if not os.path.exists(config.log_dir):
	os.mkdir(config.log_dir)
log_path = os.path.join(config.log_dir, "{}".format(config.exp_name) + '.txt')
```

如果不存在这个reps目录下的路径则make，从后续跑出来的文件来说，这里像是一个模型的存储，每次跑出来存储一个pt文件，实验是跑5次<font color='red'>（不同顺序的？）</font> ,然后每个分成10个阶段的

![image-20221123100057193](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221123100057193.png)

设置logging

```shell
# Setup logging
logging.basicConfig(
    format="%(asctime)s - %(levelname)s - %(name)s -   %(message)s",
    datefmt="%m/%d/%Y %H:%M:%S",
    handlers=[logging.StreamHandler(sys.stdout), logging.FileHandler(log_path)],
)
logger.setLevel(logging.INFO)
logger.info(config.exp_name)
```

设置tokenizer，并加入作者在论文中说的speical token

```shell
tokenizer = BertTokenizer.from_pretrained(config.bert_path, additional_special_tokens=["[E11]", "[E12]", "[E21]", "[E22]"])
```

设置record列表

```python
test_cur_record = []
test_total_record = []
```

如果是`fewrel`数据集，要加载一个`data/pid2name.json`，fewrel数据集把每种关系编号为一个比如`P126`，然后这里有一个dict的对应

```python
"P126": ["maintained by", "person or organization in charge of keeping the subject (for instance an infrastructure) in functioning order"]
```

P126，maintained by，负责保持主体（例如基础设施）正常运行的人员或组织

#### 1.1.1 开启针对于total_round（5次实验）的循环

```shell
for i in range(config.total_round)
```

##### 1.1.1.1 设置基础的记录信息和保存信息，以及seed

首先设置基础的记录信息和保存信息，以及seed <font color='red'>这里这些都要设置吗？之前感觉只有几行</font> 

```python
if not os.path.exists(f'reps/{config.exp_name}/{i}'):
    os.mkdir(f'reps/{config.exp_name}/{i}')

test_cur = []
test_total = []
set_seed(config.seed + i * 100)

def set_seed(seed):
    torch.manual_seed(seed)
    torch.cuda.manual_seed_all(seed)
    torch.cuda.manual_seed(seed)
    np.random.seed(seed)
    random.seed(seed)
    torch.backends.cudnn.benchmark = False
    torch.backends.cudnn.deterministic = True
```

##### 1.1.1.2 设置data的sampler（采样器），在`sampler.py`文件中

```shell
# sampler setup
sampler = data_sampler(config=config, seed=config.seed + i * 100, tokenizer=tokenizer)
```

- 下面是这个init的定义，初始化

```python
class data_sampler(object):

    def __init__(self, config=None, seed=None, tokenizer=None):
        """
        这是一个类（类名没有大写hh）
        """

        self.config = config

        self.tokenizer = tokenizer  # 加入了special token后的BertEncoder

        # read relation data，id2rel是一个list（id索引关系名称），rel2id是一个dict（关系名称索引id）
        self.id2rel, self.rel2id = self._read_relations(config.relation_file)

        # random sampling，看起来就是5次实验的时候，每次设置的这个任务的排列顺序都不一样
        self.seed = seed
        if self.seed != None:
            random.seed(self.seed)
        self.shuffle_index = list(range(len(self.id2rel)))  # 多少种关系
        random.shuffle(self.shuffle_index)
        self.shuffle_index = np.argsort(self.shuffle_index)

        # regenerate data
      	## 如果不存在这个cache_file（TACRED_data.pt），那么就重新生成，
        if not os.path.exists(config.cache_file):
            dataset = self._read_data(self.config.data_file)
            torch.save(dataset, config.cache_file)
        else:
            dataset = torch.load(config.cache_file)

        self.training_dataset, self.valid_dataset, self.test_dataset = dataset  # 取出来

        # generate the task number
        self.batch = 0
        self.task_length = len(self.id2rel) // self.config.rel_per_task  # 整除（下取整），多少种关系，每个任务学几个关系

        # record relations，记录关系，暂时在这里初始化的时候用不到
        self.seen_relations = []
        self.history_test_data = {}
```

- 读取关系的时候用的是下面这个函数

```python
def _read_relations(self, file):
    '''
    :param file: input relation file
    :return:  a list of relations, and a mapping from relations to their ids.
    '''
    id2rel = json.load(open(file, 'r', encoding='utf-8'))
    rel2id = {}
    for i, x in enumerate(id2rel):
        rel2id[x] = i
    return id2rel, rel2id
```

`config.relation_file`配置的是下面这个文件`data/id2rel_tacred.json`，一个列表

```shell
["org:founded_by", "per:employee_of", "org:alternate_names", "per:cities_of_residence", "per:children", "per:title", "per:siblings", "per:religion", "per:age", "org:website", "per:stateorprovinces_of_residence", "org:member_of", "org:top_members/employees", "per:countries_of_residence", "org:city_of_headquarters", "org:members", "org:country_of_headquarters", "per:spouse", "org:stateorprovince_of_headquarters", "org:number_of_employees/members", "org:parents", "org:subsidiaries", "per:origin", "org:political/religious_affiliation", "per:other_family", "per:stateorprovince_of_birth", "org:dissolved", "per:date_of_death", "org:shareholders", "per:alternate_names", "per:parents", "per:schools_attended", "per:cause_of_death", "per:city_of_death", "per:stateorprovince_of_death", "org:founded", "per:country_of_birth", "per:date_of_birth", "per:city_of_birth", "per:charges"]
```

原来是通过id索引关系（id2rel，是一个list），现在制作一个反向的mapping通过关系索引id（rel2id，是一个dict）

- `_read_data`的逻辑是下面这样的

加载的数据文件是`config.data_file`，也就是`data/data_with_marker_tacred.json`，这个数据格式化看起来是这样的，作者按照这个关系，比如说`org:founded_by`进行第一层级的建模，然后是已经把tacred数据集进行与处理后了（所以可能可以公开发布出来吧），这里比如`[E21]/[E22]`这些special token就和论文里面说的一样

![image-20221123110136151](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221123110136151.png)

<font color='red'>这里因为是按照关系来的，对于CRE任务来说不会出现串了的情况</font> 

```python
def _read_data(self, file):
    '''
    :param file: the input sample file
    :return: samples for the model: [relation label, text]
    '''
    data = json.load(open(file, 'r', encoding='utf-8'))
    train_dataset = [[] for i in range(self.config.num_of_relation)]  # TACRED 40个
    val_dataset = [[] for i in range(self.config.num_of_relation)]  # TACRED 40种关系
    test_dataset = [[] for i in range(self.config.num_of_relation)]

    # random sample 40 samples for test, and 320 samples for train（这个是对于每个关系而来的）
    for relation in tqdm(data.keys(), desc="Load data"):  # TACRED 40个
        rel_samples = data[relation]  # 属于这个关系的所有类都取出来，
        if self.seed != None:
            random.seed(self.seed)
        random.shuffle(rel_samples)  # 随机shuffle属于这个关系的列表，很多条数据，随机打乱保证了随机性
        count = 0
        count1 = 0
        for i, sample in enumerate(rel_samples):
            tokenized_sample = {}
            tokenized_sample['relation'] = self.rel2id[sample['relation']]  # 转成之前关系->id对应的id（rel2id是dict）
            tokenized_sample['tokens'] = self.tokenizer.encode(' '.join(sample['tokens']))  # 空格连接，然后encode
            tokenized_sample['string'] = '[RelData] ' + sample['relation'] + ' ' + ' '.join(sample['tokens'])  # 拼接出一个 [RelData] + 关系 + tokens的
           
            if self.config.task_name == 'FewRel':
                if i < self.config.num_of_train:
                    train_dataset[self.rel2id[relation]].append(tokenized_sample)
                elif i < self.config.num_of_train + self.config.num_of_val:
                    val_dataset[self.rel2id[relation]].append(tokenized_sample)
                else:
                    test_dataset[self.rel2id[relation]].append(tokenized_sample)
            else:  # TACRED，只分train和test，作者说的是40个用来test，320个用来train，这里就是直接强制
                if i < len(rel_samples) // 5 and count <= 40:  # 这个<=有bug，应该是<才对吧，不知道这里和其他有没有对齐
                    count += 1
                    test_dataset[self.rel2id[relation]].append(tokenized_sample)
                else:
                    count1 += 1
                    train_dataset[self.rel2id[relation]].append(tokenized_sample)
                    if count1 >= 320:
                        break
    return train_dataset, val_dataset, test_dataset  # 返回三个dataset
```

这里制作了dataset之后，会存成一个pt文件，下次用到的时候不用再制作了，直接读取pt文件就可以

- 重定义`data_sampler`类的`__iter__`与`__next__`

```python
def __iter__(self):
    return self

def __next__(self):

    if self.batch == self.task_length:
        raise StopIteration()
	# self.config.rel_per_task是4（每个task relation 4个），每次截断这个batch区间内的4个关系（也就是分成10个task的过程？）
    # 注意这里是shuffle_index，已经把relation给打乱了
    indices = self.shuffle_index[self.config.rel_per_task*self.batch: self.config.rel_per_task*(self.batch+1)]
    self.batch += 1

    # 在每次next的时候会清空
    current_relations = []
    cur_training_data = {}
    cur_valid_data = {}
    cur_test_data = {}

    for index in indices:
        current_relations.append(self.id2rel[index])  # 当前有哪些关系
        self.seen_relations.append(self.id2rel[index])  # seen_relations是整体的，已经见过哪些了

        cur_training_data[self.id2rel[index]] = self.training_dataset[index]
        cur_valid_data[self.id2rel[index]] = self.valid_dataset[index]
        cur_test_data[self.id2rel[index]] = self.test_dataset[index]
        self.history_test_data[self.id2rel[index]] = self.test_dataset[index]  # 把test给加进来

    return cur_training_data, cur_valid_data, cur_test_data, current_relations, self.history_test_data, self.seen_relations
```

##### 1.1.1.3 传出来id2rel, rel2id，然后初始化一个BertEncoder

```python
id2rel = sampler.id2rel
rel2id = sampler.rel2id
encoder = Bert_Encoder(config=config).cuda()
```

这里BertEncoder是一个整体的大结构，作者自己实现了换一下，不只是一个普通的层

```shell
class Bert_Encoder(base_model):
#  load_config.attention_probs_dropout_prob = config.monto_drop_ratio
    # load_config.hidden_dropout_prob = config.monto_drop_ratio
    def __init__(self, config, attention_probs_dropout_prob=None, hidden_dropout_prob=None, drop_out=None): 
        super(Bert_Encoder, self).__init__()

        # load model，加载模型，encoder和bert_config
        self.encoder = BertModel.from_pretrained(config.bert_path).cuda()
        self.bert_config = BertConfig.from_pretrained(config.bert_path)

        # for monto kalo
        # 这里感觉有3个能设置的dropout比率，两个bert内部的，一个自己的dropout层
        if attention_probs_dropout_prob is not None:
            assert hidden_dropout_prob is not None and drop_out is not None
            self.bert_config.attention_probs_dropout_prob = attention_probs_dropout_prob
            self.bert_config.hidden_dropout_prob = hidden_dropout_prob
            config.drop_out = drop_out

        # the dimension for the final outputs，硬编码在这里了，输出是768的
        self.output_size = 768

        self.drop = nn.Dropout(config.drop_out)  # 自己的一个dropout层

        # find which encoding is used
        # 这里相当于是一个作者自己的实现，在config里面默认设置的是entity_marker这个
		# entity_marker就是加载作者这个自己处理后的TACRED数据集，带着speical token的（[E11]/[E12]/[E21]/[E22]）
        if config.pattern in ['standard', 'entity_marker']:
            self.pattern = config.pattern
        else:
            raise Exception('Wrong encoding.')
		# 如果是加上这些special token，就+4个，然后最后是2*hidden_size到output_size
        if self.pattern == 'entity_marker':  
            self.encoder.resize_token_embeddings(config.vocab_size + 4)
            self.linear_transform = nn.Linear(self.bert_config.hidden_size*2, self.output_size, bias=True)
        else:
            self.linear_transform = nn.Linear(self.bert_config.hidden_size, self.output_size, bias=True)
     	# layer norm
        self.layer_normalization = nn.LayerNorm([self.output_size])


    def get_output_size(self):
        return self.output_size  # 输出output_size

    def forward(self, inputs):
        '''
        :param inputs: of dimension [B, N]  # 前向传播 [batch_size, seq_len]
        :return: a result of size [B, H*2] or [B, H], according to different strategy
        '''
        # generate representation under a certain encoding strategy
        if self.pattern == 'standard':
            # in the standard mode, the representation is generated according to
            #  the representation of[CLS] mark.
            output = self.encoder(inputs)[1]
        else:  # 现在是走的这个逻辑，因为这个是作者的ACA逻辑，加入了special token，作者的数据已经处理好了
            # in the entity_marker mode, the representation is generated from the representations of
            #  marks [E11] and [E21] of the head and tail entities.
            e11 = []  # 第一个实体的start
            e21 = []  # 第二个实体的start
            # for each sample in the batch, acquire the positions of its [E11] and [E21]
            # 对于每个batch中的sanple
            for i in range(inputs.size()[0]):  # 
                tokens = inputs[i].cpu().numpy()  # 每个sample
                e11.append(np.argwhere(tokens == 30522)[0][0])  # 0-30511（30522个），之后是按照顺序的加入"[E11]522", "[E12]523", "[E21]524", "[E22]525"
                e21.append(np.argwhere(tokens == 30524)[0][0])

            # input the sample to BERT
            attention_mask = inputs != 0
            tokens_output = self.encoder(inputs, attention_mask=attention_mask)[0] # [B,N] --> [B,N,H] hidden_size
            output = []

            # for each sample in the batch, acquire its representations for [E11] and [E21]
            for i in range(len(e11)):
                instance_output = torch.index_select(tokens_output, 0, torch.tensor(i).cuda())
                instance_output = torch.index_select(instance_output, 1, torch.tensor([e11[i], e21[i]]).cuda())
                output.append(instance_output) # [B,N] --> [B,2,H] 把[E11] [E21]的embedding给拿出来

            # for each sample in the batch, concatenate the representations of [E11] and [E21], and reshape
            # 把这两个concate后，消掉一个维度
            output = torch.cat(output, dim=0)
            output = output.view(output.size()[0], -1) # [B,N] --> [B,H*2]
            
            # the output dimension is [B, H*2], B: batchsize, H: hiddensize
            output = self.drop(output)
            output = self.linear_transform(output)
            output = F.gelu(output)
            output = self.layer_normalization(output) 
        return output  # E11和E21的表征，用这两个表征来判断关系？
```

##### 1.1.1.4 针对ACA的一些配置处理，并且建立proto_softmax_layer，设置memorized_samples字典memorized_samples = {}

```python
if config.aca:
    add_relation_num = config.rel_per_task * 3  # 每个是4种关系，这里变成12，
    model = proto_softmax_layer(
    encoder, 
    num_class = len(sampler.id2rel) + add_relation_num,   # 40种关系，加上12种关系？
    id2rel = sampler.id2rel, 
    drop = 0, 
    config = config).cuda()
else:
    model = proto_softmax_layer(
        encoder, 
        num_class = len(sampler.id2rel), 
        id2rel = sampler.id2rel, 
        drop = 0, 
        config = config).cuda()
```

- 内部的这个模型（emar）的复现，这里大概来说一下，主要分为`base_model`类还有`proto_softmax_layer`类

```python
class base_model(nn.Module):

    def __init__(self):
        """
        0和pi的常数
        """
        super(base_model, self).__init__()
        self.zero_const = nn.Parameter(torch.Tensor([0]))
        self.zero_const.requires_grad = False
        self.pi_const = nn.Parameter(torch.Tensor([3.14159265358979323846]))
        self.pi_const.requires_grad = False

    def save_checkpoint(self, path):
        """
        保存checkpoint
        """
        torch.save(self.state_dict(), path)

    def load_checkpoint(self, path):
        """
        加载checkpoint，并且设置为evaluate模式
        """
        self.load_state_dict(torch.load(os.path.join(path)))
        self.eval()

    def save_parameters(self, path):
        f = open(path, "w")
        f.write(json.dumps(self.get_parameters("list")))
        f.close()

    def load_parameters(self, path):
        f = open(path, "r")
        parameters = json.loads(f.read())
        f.close()
        for i in parameters:
            parameters[i] = torch.Tensor(parameters[i])
        self.load_state_dict(parameters, strict=False)
        self.eval()

    def get_parameters(self, mode="numpy", param_dict=None):
        all_param_dict = self.state_dict()
        if param_dict == None:
            param_dict = all_param_dict.keys()
        res = {}
        for param in param_dict:
            if mode == "numpy":
                res[param] = all_param_dict[param].cpu().numpy()
            elif mode == "list":
                res[param] = all_param_dict[param].cpu().numpy().tolist()
            else:
                res[param] = all_param_dict[param]
        return res

    def set_parameters(self, parameters):
        for i in parameters:
            parameters[i] = torch.Tensor(parameters[i])
        self.load_state_dict(parameters, strict = False)
        self.eval()

class proto_softmax_layer(base_model):
    """
    Softmax classifier for sentence-level relation extraction.
    """

    def __distance__(self, rep, rel):
        """
        算一个距离，点乘后求和？
        """
        rep_ = rep.view(rep.shape[0], 1, rep.shape[-1])
        rel_ = rel.view(1, -1, rel.shape[-1])
        dis = (rep_ * rel_).sum(-1)
        return dis

    def __init__(self, sentence_encoder, num_class, id2rel, drop = 0, config = None, rate = 1.0):
        """
        Args:
            sentence_encoder: encoder for sentences
            num_class: number of classes，在emar-aca中扩充到40 + 12个类别了
            id2rel: dictionary of id -> relation name mapping
        """
        super(proto_softmax_layer, self).__init__()

        self.config = config
        self.sentence_encoder = sentence_encoder
        self.num_class = num_class
        self.hidden_size = self.sentence_encoder.output_size
        self.fc = nn.Linear(self.hidden_size, self.num_class, bias = False)  # hidden -> num_class
        self.drop = nn.Dropout(drop)
        self.id2rel = id2rel
        self.rel2id = {}
        # 这里又反向搞了一回
        for id, rel in enumerate(id2rel):
            self.rel2id[rel] = id

    def incremental_learning(self, old_class, add_class):
        """
        增量学习，
        """
        weight = self.fc.weight.data
        self.fc = nn.Linear(768, old_class + add_class, bias=False).cuda()
        with torch.no_grad():
            self.fc.weight.data[:old_class] = weight[:old_class]

    def set_memorized_prototypes(self, protos):
        self.prototypes = protos.detach().cuda()
   
    def get_feature(self, sentences):
        rep = self.sentence_encoder(sentences)
        return rep.cpu().data.numpy()

    def get_mem_feature(self, rep):
        dis = self.mem_forward(rep)
        return dis
    
    def forward(self, sentences):
        """
        Args:
            args: depends on the encoder
        Return:
            logits, (B, N)
        """
        rep = self.sentence_encoder(sentences) # (B, H) 只输出那个E11 E21加权后的
        rep = self.drop(rep)  # 这里又drop一次？之前sentence_encoder感觉在最后drop了一下了
        logits = self.fc(rep)  # 全连接
        return logits, rep

    def mem_forward(self, rep):
        """
        Args:
            args: depends on the encoder
        Return:
            logits, (B, N)
        """
        dis_mem = self.__distance__(rep, self.prototypes)
        return dis_mem
```

<font color='red'>这里的add_relation_num实在是不太理解，为什么会是12呢</font> 

<font color='red'>对于fewrel应该是12种，（因为每次8个，8+4），那tacred会不会这里有bug？</font> 

#### 1.1.2 开启对于sampler的循环（10个Task）

这里sampler之前已经处理好了，定义了是4个4个的这样的

```python
for episode, (training_data, valid_data, test_data, current_relations, historic_test_data, seen_relations) in enumerate(sampler):
    """
    training/valid/test_data：针对当前这些关系的训练/验证/测试集合
    current_relations：当前的关系
    historic_test_data：随着episode不断的把test给加进来
    seen_relations：见过的关系
    
    注意，当前的算作见过和test中
    """
    print(current_relations)  # 本次的关系
  	

    train_data_for_initial = []  # 每次初始化一个新的
    for relation in current_relations:
        train_data_for_initial += training_data[relation]  # 那边的是一个dict，把本次的关系给加进来
    # Step1-2: Initialize: Just Random Initialize the as other model
    
    # 这个是作者论文的处理部分，如果是aca的，调用这个get_aca_data方法
    if config.aca:
        # 获得数据，并且多出rel_per_task + rel_per_task//2种关系
        add_aca_data = get_aca_data(config, deepcopy(training_data), current_relations, tokenizer)

    # Step3 -> Step5
    # 预训练了一手？
    if config.aca:
        # 数据使用model训练两个epoch，把新增数据混进去
        logger.info(f'data num for step 1: {len(train_data_for_initial + add_aca_data)}')
        model = train_simple_model(config, model, train_data_for_initial + add_aca_data, 2) 
    else:
        logger.info(f'data num for step 1: {len(train_data_for_initial)}')
        model = train_simple_model(config, model, train_data_for_initial, 2) 
        
    if config.aca:
        """
        def incremental_learning(self, old_class, add_class):
            weight = self.fc.weight.data
            self.fc = nn.Linear(768, old_class + add_class, bias=False).cuda()
            with torch.no_grad():
                self.fc.weight.data[:old_class] = weight[:old_class]  # 把之前的复制进来构建这个层增量？
        """
        logger.info('remove aca node')
        model.incremental_learning(config.num_of_relation, add_relation_num)  # 不理解为什么add_relation_num是config.rel_per_task * 3

    # Step6，记录下来一些数据
    logger.info('Selecting Examples for Memory')
    for relation in current_relations:
        memorized_samples[relation] = select_data(config, encoder, training_data[relation])

    # Step7
    mem_data = []
    for rel in memorized_samples:
        mem_data += memorized_samples[rel]
    
    
    # Step8: Ak，把记录下来的数据，和老的数据混到一块训？
    data4step2 = mem_data + train_data_for_initial 

    logger.info('Replay, Activation and Reconsolidation')  # 重放训练
    seen_relation_ids = [rel2id[rel] for rel in seen_relations]

    # Step9
    for _ in range(2):

        # Step10 - 12 (use all to compute proto)
        protos4train = []
        for relation in seen_relations:
            protos4train.append(get_proto(config, encoder, memorized_samples[relation]))
        protos4train = torch.cat(protos4train, dim=0).detach()

        # Step13 - 15
        print('Memory Replay and Activation')
        model = train_simple_model(config, model, data4step2, 1) # Memory Replay and Activation

        # Step16 - 18 use all Memory Example; balance
        print("Memory Reconsolidation")
        model = train_model(config, model, mem_data, 1, protos4train, seen_relation_ids) # Memory Reconsolidaton

    protos4eval = []
    for relation in seen_relations:
        r = model.fc.weight[rel2id[relation]].detach()
        proto = get_proto(config, encoder, memorized_samples[relation], r)
        proto = proto / proto.norm()
        protos4eval.append(proto)
    protos4eval = torch.cat(protos4eval, dim=0).detach()

    model.set_memorized_prototypes(protos4eval)

    print('[Evaluation]')
    test_data_1 = []
    """
    current_relations：新增加进来的几个relation
    """
    for relation in current_relations:
        test_data_1 += test_data[relation]

    test_data_2 = []
    """
    在所有见过的relation上测
    """
    for relation in seen_relations:
        test_data_2 += historic_test_data[relation]
    cur_acc = evaluate_strict_model(config, test_data_1,seen_relations, rel2id, mode="cur", pid2name=pid2name, model=model)
    total_acc =  evaluate_strict_model(config, test_data_2 ,seen_relations, rel2id, mode="total", logger=logger, pid2name=pid2name, model=model)
    
    save_representation_to_file(config, model, sampler, id2rel, f'reps/{config.exp_name}/{i}/{episode}.pt' ,memorized_samples)

  
    logger.info(f'Restart Num {i+1}')
    logger.info(f'task--{episode + 1}:')
    test_cur.append(cur_acc)
    test_total.append(total_acc)
    logger.info(f'history test acc:{test_total}')
    logger.info(f'current test acc:{test_cur}')
```

##### 1.1.2.1 详解get_aca_data函数

```python
def get_aca_data(config, training_data, current_relations, tokenizer):
    """
    config，训练集，当前的关系，tokenzier（加入了special token的tokenizer）
    """
    rel_id = config.num_of_relation  # 40
    aca_data = []
    # 把当前的current_relations（只针对这每个task的增量，比如每次新来4个，这样拆成两半）
    for rel1, rel2 in zip(current_relations[:config.rel_per_task // 2], current_relations[config.rel_per_task // 2:]):
        datas1 = training_data[rel1]  # 关系i
        datas2 = training_data[rel2]  # 关系j
        L = 5  # 超参数，前后5个词
        for data1, data2 in zip(datas1, datas2):  # 这是两边关系的数据，因为训练集采样320了，那么肯定是不会对不齐的？
            # 对token1进行处理
            token1 = data1['tokens'][1:-1][:]  # 掐头去尾，self.tokenizer.encode可能会把CLS和SEP给带上
            # 找位置，在token1中
            e11 = token1.index(30522); e12 = token1.index(30523)
            e21 = token1.index(30524); e22 = token1.index(30525)
            if e21 <= e11:
                # 这个处理不是太明白，应该是一个前到后的关系？
                continue
            # 截取下来一段，只要e11的前后5个词或者一个容错处理这样的感觉
            token1_sub = token1[max(0, e11-L): min(e12+L+1, e21)]
			
            # token2做相同的处理，最后保留e21前后5个词这样的感觉
            token2 = data2['tokens'][1:-1][:]
            e11 = token2.index(30522); e12 = token2.index(30523)
            e21 = token2.index(30524); e22 = token2.index(30525)
            if e21 <= e11:
                continue

            token2_sub = token2[max(e12+1, e21-L): min(e22+L+1, len(token2))]
			
            # 再把CLS和SEP给拼回来，然后把这俩强行拼到一起
            token = [101] + token1_sub + token2_sub + [102]
            aca_data.append({
                'relation': rel_id,
                'tokens': token,
                'string': tokenizer.decode(token)
            })

            for index in [30522, 30523, 30524, 30525]:
                assert index in token and token.count(index) == 1
                
        rel_id += 1  # 这里从40开始，每次新加一个类，假设来说的话，每次新来4个类，就会多加出2个类来

    for rel in current_relations:
        # 这个是无向的关系集合，不知道是做了统计还是怎么的
        if rel in ['P26', 'P3373', 'per:siblings', 'org:alternate_names', 'per:spous', 'per:alternate_names', 'per:other_family']:
            continue
		
        # 这里使用index然后反过来替换，制作反向的关系，假设4个关系就多出4个没用的
        for data in training_data[rel]:
            token = data['tokens'][:]
            e11 = token.index(30522); e12 = token.index(30523)
            e21 = token.index(30524); e22 = token.index(30525)
            token[e11] = 30524; token[e12] = 30525
            token[e21] = 30522; token[e22] = 30523

            aca_data.append({
                    'relation': rel_id,
                    'tokens': token,
                    'string': tokenizer.decode(token)
                })
            for index in [30522, 30523, 30524, 30525]:
                assert index in token and token.count(index) == 1
        rel_id += 1
    return aca_data  # 增强后的数据，这里多出来了不少数据，以及N+N//2种关系，这个算是作者的主要创新部分吗？
```

##### 1.1.2.2 train_simple_model函数

```python
def train_simple_model(config, model, train_set, epochs, step2=False):
    data_loader = get_data_loader(config, train_set, shuffle=True)
    model.train()
    criterion = nn.CrossEntropyLoss()
    optimizer = optim.Adam([
                            {'params': model.sentence_encoder.parameters(), 'lr': 0.00001},
                            {'params': model.fc.parameters(), 'lr': 0.001}
                            ])
    
    for epoch_i in range(epochs):
        losses = []
        for step, (_, labels, sentences, _, _) in enumerate(tqdm(data_loader)):
            model.zero_grad()
            sentences = torch.stack([x.to(config.device) for x in sentences], dim=0)
            logits, _ = model(sentences)
            if step2 is True:
                logits = logits[:, :config.num_of_relation]
            labels = labels.cuda()
            loss = criterion(logits, labels)
            loss.backward()
            losses.append(loss.item())
            torch.nn.utils.clip_grad_norm_(model.parameters(), config.max_grad_norm)
            optimizer.step()
        print (np.array(losses).mean())
    return model
```

#### 1.1.3 收尾（对齐1.1.1）

```shell
test_cur_record = torch.tensor(test_cur_record)
test_total_record = torch.tensor(test_total_record)

test_cur_record = torch.mean(test_cur_record, dim=0).tolist()
test_total_record = torch.mean(test_total_record, dim=0).tolist()

logger.info(f'Avg current test acc: {test_cur_record}')
logger.info(f'Avg total test acc: {test_total_record}')

print("log file has been saved in: ", log_path)
```

## 2. 代码中阅读到的问题和重点记录

- 作者使用的是special token的方式，依次加入`[E11] [E12] [E21] [E22]`，bert的词表大小是30522，所以开始的编号是0-30511的，这几个token也就依次被定位为30522, 30523, 30524, 30525这样的；
- 对于这些special token的加入，作者是在预处理数据的时候就完成了，数据预处理的时候是先用relation type对应到各个样例，这样relation type在json文件中是第一层，就可以让持续学习比较好处理这种文件；
- config文件和shell文件中的下面这几个参数，是针对fewrel数据集来用的，而TACRED数据集是定死的，训练集340，测试集40，没有验证集；

```python
parser.add_argument('--num_of_train', default=420, type=int)
parser.add_argument('--num_of_val', default=140, type=int)
parser.add_argument('--num_of_test', default=140, type=int)
```

- 作者在划分数据集的时候这里代码有一点bug，这里因为是<=的符号，所以最后出来的都是41个数据，也不知道是不是他对齐的工作都是这么操作的，这个从作者输出的confusion matrix中也能看出来

https://github.com/Wangpeiyi9979/ACA/blob/898238415202d4bd6b7b555660b82732ab03483e/sampler.py#L114

```shell
if i < len(rel_samples) // 5 and count <= 40:
    count += 1
    test_dataset[self.rel2id[relation]].append(tokenized_sample)
else:
    count1 += 1
    train_dataset[self.rel2id[relation]].append(tokenized_sample)
    if count1 >= 320:
        break
```

```shell
+----------+----------+----------+---------+----------+
|          | per:scho | per:orig | per:age | per:stat |
+----------+----------+----------+---------+----------+
| per:scho | 35       | 0        | 0       | 0        |
+----------+----------+----------+---------+----------+
| per:orig | 0        | 40       | 0       | 1        |
+----------+----------+----------+---------+----------+
| per:age  | 0        | 0        | 41      | 0        |
+----------+----------+----------+---------+----------+
| per:stat | 1        | 1        | 0       | 39       |
+----------+----------+----------+---------+----------+
```

- 在实现ACA的方法的时候，作者写的还是和论文里差不多的，论文里说用第一种关系重建生成N//2种，用第二种反向生成N种，在代码里看起来这是最理想的情况，如果遇到一些特殊情况，或者反向的时候是那种对称的关系，那么就可能不到这个种类数目；
- 另外在代码这里涉及到一个model.incremental_learning，这个地方感觉不是很理解，他这个add_class的数目是3倍的`rel_per_task`，那么对于TACRED每个task新进来4个关系的话，这个add_relation_num就变成12，加起来就是40+12=52种关系分类，但是每一次最多也就是40 + 4 + 2种关系的分类，这种在训练的过程里，是需要模型来学习这种关系吗？因为感觉模型的fc层，不是从4 8 12 16这样一直加上来的；；；另外这里也没理解这个`with torch.no_grad()`的作用；

```python
if config.aca:
    """
    def incremental_learning(self, old_class, add_class):
        weight = self.fc.weight.data
        self.fc = nn.Linear(768, old_class + add_class, bias=False).cuda()
        with torch.no_grad():
            self.fc.weight.data[:old_class] = weight[:old_class]  # 把之前的复制进来构建这个层增量？
    """
    logger.info('remove aca node')
    model.incremental_learning(config.num_of_relation, add_relation_num)  # 不理解为什么add_relation_num是config.rel_per_task * 3
```

- 反向关系也直接定义成一种新的关系，像群里师兄说的，这个关系会不会没什么意义，而很多反向关系是可以在数据集中找到的，不知道这些数据集中有没有对反向关系的定义，如果我开始学习了B->A是一种反向新定义的没有含义的关系，但是B->A这个关系在之后训练出现了，那感觉就很容易分类错误？
- 那种拼接的方式也确实像师兄说的比较粗暴，看起来只是怎么取了5个词，强行给拼到一块上一样；

- cur_acc就是在当前task的几个关系上的F1-Score，total_acc就是在当前+之前见过的所有关系上的F1-score，看起来作者主表格中挂的应该是total_acc

```python
test_data_1 = []
"""
current_relations：新增加进来的几个relation
"""
for relation in current_relations:
test_data_1 += test_data[relation]

test_data_2 = []
"""
在所有见过的relation上测
"""
for relation in seen_relations:
test_data_2 += historic_test_data[relation]
cur_acc = evaluate_strict_model(config, test_data_1,seen_relations, rel2id, mode="cur", pid2name=pid2name, model=model)
total_acc =  evaluate_strict_model(config, test_data_2 ,seen_relations, rel2id, mode="total", logger=logger, pid2name=pid2name, model=model)

save_representation_to_file(config, model, sampler, id2rel, f'reps/{config.exp_name}/{i}/{episode}.pt' ,memorized_samples)
```

- 一般来说，学习的顺序和采样的情况都会影响，作者就是很多随机策略，每次换一个seed这样的，5次实验，每次跑10个task，然后比如TACRED就是随机打乱后采样320个训练数据加40个测试数据；

- 看起来像是一个train_simple_model + train_model的学习范式，在train_simple_model的时候把aca的数据混进去，但好像后续就不需要了（对EMAR还没了解太多）；

