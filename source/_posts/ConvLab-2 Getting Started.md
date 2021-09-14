---
title: ConvLab-2 Getting Started
date: 2021-09-14 20:01:01
tags:
    - NLP
    - Dialogue
categories:
	- NLP
---

# ConvLab-2 Getting Started

ConvLab-2.ipynb

ConvLab-2是一个开源工具包，使研究人员能够使用最先进的模型构建面向任务的对话系统，执行端到端评估，并诊断系统的弱点。作为ConvLab的继承者，ConvLab-2继承了ConvLab的框架，但集成了更强大的对话模型并支持更多的数据集。此外，作者还开发了一个分析工具和一个交互式工具，以帮助研究人员诊断（diagnosing）对话系统。

从这里学习一些对话系列的模型，服务器上操作遇到一些问题，目前还是用google colab操作的

Reference：
> https://github.com/thu-coai/ConvLab-2

<!--more-->

## 1. 初始化准备，installation

git clone 自己fork git的内容（**注意在colab环境下的修改一定要同步到本地中，或者说每次都是本地改完想办法传到colab/github上**）
```shell
!git clone https://github.com/yixuan004/ConvLab-2.git && cd ConvLab-2 && pip install -e .
```

安装spacy使用的en_core_web_sm，来解决BERTNLU中的内容
```shell
!python -m spacy download en_core_web_sm
```

## 2. build an agent

这里感觉是构建一个机器自动应答的pipeline，包括NLU，DST，Policy和NLG这些模块，组合在一起可以作为机器端的对话机器的感觉

```python
from convlab2.nlu.jointBERT.multiwoz import BERTNLU
from convlab2.nlu.milu.multiwoz import MILU
from convlab2.dst.rule.multiwoz import RuleDST
from convlab2.policy.rule.multiwoz import RulePolicy
from convlab2.nlg.template.multiwoz import TemplateNLG
from convlab2.dialog_agent import PipelineAgent, BiSession
from convlab2.evaluator.multiwoz_eval import MultiWozEvaluator
from pprint import pprint
import random
import numpy as np
import torch

print("import done!")
```

建立模型并建造一个agent
```python
# go to README.md of each model for more information
# BERT nlu，understanding
sys_nlu = BERTNLU()
# simple rule DST，tracking
sys_dst = RuleDST()
# rule policy，rule
sys_policy = RulePolicy()
# template NLG，生成
sys_nlg = TemplateNLG(is_user=False)
# assemble，集成到一个pipeline里
sys_agent = PipelineAgent(sys_nlu, sys_dst, sys_policy, sys_nlg, name='sys')

print("build agent done!")
```

和agent进行手动输入对话测试
```python
# 对话1
sys_agent.response("I want to find a moderate hotel")
>>> We have 18 such places . How about bridge guest house ? Fits your request perfectly .

sys_agent.response("Which type of hotel is it ?")
>>> It is a guesthouse .

sys_agent.response("OK , where is its address ?")
>>> It is located at 124 tenison road.

sys_agent.response("Thank you !")
>>> Thank you for contacting the help desk . Have a great day .
```

```python
# 对话2
sys_agent.response("Try to find me a Chinese restaurant in south area .")
>>> There are 3 different places that match your description . The lucky star has some great reviews . How about chinese ?

sys_agent.response("Which kind of food it provides ?")
>>> They serve chinese .

sys_agent.response("Book a table for 5 , this Sunday .")
>>> Your reference number is 00000003 .
```

## 3. Build a simulator to chat with the agent and evaluate
构建一个模拟器与代理聊天并评估

- 在许多一对一面向任务的对话系统中，模拟器对于训练RL代理是必不可少的。在我们的框架中，我们不区分用户和系统。所有发言者都是代理人。模拟器也是一个代理，内部有特定的策略以实现用户目标。

- 我们对模拟器使用Agenda策略，该策略需要对话行为输入，这意味着我们应该将PipelineAgent的DST参数设置为None。然后PipelineAgent将直接将对话act传递给策略。有关更多详细信息，请参阅PipelineAgent文档。（这里不是很明白为什么要把user_policy设置为None）

这里是模拟器，因为是模拟器所以代表了用户，这样在交互的过程中并不需要跟踪对话状态，所以不需要DST？

```python
# MILU
user_nlu = MILU()
# not use dst
user_dst = None
# rule policy
user_policy = RulePolicy(character='usr')
# template NLG
user_nlg = TemplateNLG(is_user=True)
# assemble
user_agent = PipelineAgent(user_nlu, user_dst, user_policy, user_nlg, name='user') # user的agent！
```
现在我们有了一个simulator和一个agent。我们将使用现有的简单的一对一会话控制器BiSession，还可以定义自己的Session class来应对自己的特殊需求

我们添加了MultiWozEvaluator来评估性能。它使用的解析的对话框act input 和策略输出对话框act来计算inform f1、book rate以及任务是否成功
```python
evaluator = MultiWozEvaluator()
sess = BiSession(sys_agent=sys_agent, user_agent=user_agent, kb_query=None, evaluator=evaluator)
```

让这两个代理进行会话！key是BiSession类的next_turn这个方法
```python
def set_seed(r_seed):
    random.seed(r_seed)
    np.random.seed(r_seed)
    torch.manual_seed(r_seed)

set_seed(20210914)

sys_response = ''
sess.init_session()
print('init goal:')
pprint(sess.evaluator.goal)
print('-'*50)
for i in range(20):
    sys_response, user_response, session_over, reward = sess.next_turn(sys_response)
    print('user:', user_response)
    print('sys:', sys_response)
    print()
    if session_over is True:
        break
print('task success:', sess.evaluator.task_success())
print('book rate:', sess.evaluator.book_rate())
print('inform precision/recall/f1:', sess.evaluator.inform_F1())
print('-'*50)
print('final goal:')
pprint(sess.evaluator.goal)
print('='*100)

>>> init goal:
{'attraction': {'info': {'area': 'east'},
                'reqt': {'address': '?', 'entrance fee': '?', 'phone': '?'}}}
--------------------------------------------------
user: I am planning a trip to town and want to sight see a bit . Can you let me know some attractions I may be interested in ? Can you recommend some attractions in the east ?
sys: There are 10 , anything in particular you are looking for ? I recommend saint barnabas press gallery. They are located at the belfast yard, coldham's road .

user: Sounds good . Could I get the phone number of the attraction ? Can you let me know what the entrance fee is too ?
sys: The attraction phone number is 01223902088 . Its entrance fee is ? .

user: Does it have an entrance fee ?
sys: Its entrance fee is ? .

user: Does it have an entrance fee ?
sys: Their entrance fee is ? by our system currently .

user: Does it have an entrance fee ?
sys: Their entrance fee is ? by our system currently .

user: I will need the entrance fee please .
sys: Its entrance fee is ? .

user: I would like to know the entrance fee .
sys: Their entrance fee is ? by our system currently .

user: What are the entrance fees ?
sys: The entry fee is ? .

user: Can you tell me the entrance fee ?
sys: Their entrance fee is ? by our system currently .

user: Does it have an entrance fee ?
sys: Their entrance fee is ? by our system currently .

user: Does it have an entrance fee ?
sys: Its entrance fee is ? .

user: Is there an entrance fee ?
sys: The entry fee is ? .

user: Can you let me know what the entrance fee is too ?
sys: The entry fee is ? .

user: Can you tell me the entrance fee ?
sys: The entry fee is ? .

user: I will need the entrance fee please .
sys: Their entrance fee is ? by our system currently .

user: I would like to know the entrance fee .
sys: Their entrance fee is ? by our system currently .

user: I just need to know how much the entrance fee is .
sys: Their entrance fee is ? by our system currently .

user: Can you give me the entrance fee ?
sys: Their entrance fee is ? by our system currently .

user: Can you let me know what the entrance fee is too ?
sys: The entry fee is ? .

user: Does it have an entrance fee ?
sys: Its entrance fee is ? .

task success: 0
book rate: None
inform precision/recall/f1: (1.0, 0.6666666666666666, 0.8)
--------------------------------------------------
final goal:
{'attraction': {'info': {'area': 'east'},
                'reqt': {'address': "the belfast yard, coldham's road",
                         'entrance fee': '?',
                         'phone': '01223902088'}}}
====================================================================================================
```

## 还有一些其他简易操作，例如Try different module combinations，Use analysis tool to diagnose the system，见.ipynb中的整理了