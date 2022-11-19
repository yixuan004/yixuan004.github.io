---
title: 记录服务器conda虚拟环境导出
date: 2022-03-23 15:29:16
tags:
    - macOS基础操作
categories:
	- Crsenal
---

某些场景下需要进行虚拟环境迁移，或者使用其他人的虚拟环境，这种时候如果能够完整导出别人的虚拟环境，在自己的环境下直接下载同步就很方便。

<!--more-->

Reference:
> https://blog.csdn.net/shunaoxi2313/article/details/92003710


# 原机器A的导出准备

通过如下命令，可以查看当前conda的可用环境
```shell
(base) abc@h1:~$ conda info --envs
# conda environments:
#
base                  *  /home/abc/miniconda3
```

导出当前的conda环境（注意，这样导出貌似只会导出使用conda命令安装的内容，如果需要导出pip则还需要其他步骤），这里直接在base环境下（如果要导出别的，就conda activate venv1这类的之后再导出就可以）。导出后会在目录下生成一个py36.yaml文件
```shell
conda env export > py36.yaml
```

**注意**如果导出base环境的话，在目标机器上可能会提示已存在base环境（而且base环境无法删除）。所以如果导出base环境最好先复制一下，再导出base环境的复制品，如下所示
```shell
(base) abc@h1:~$ conda create -n h1_abc_base --clone base
Source:      /home/abc/miniconda3
Destination: /home/abc/miniconda3/envs/h1_abc_base
The following packages cannot be cloned out of the root environment:
 - defaults/linux-64::conda-4.9.2-py36h06a4308_0
Packages: 84
Files: 35464

Preparing transaction: done
Verifying transaction: done
Executing transaction: / Enabling nb_conda_kernels...
Status: enabled
done
#
# To activate this environment, use
#
#     $ conda activate h1_abc_base
#
# To deactivate an active environment, use
#
#     $ conda deactivate
```

```shell
(base) abc@h1:~$ conda activate h1_abc_base
(h1_abc_base) abc@h1:~$ conda env export > h1_abc_base.yaml
```

完成上述步骤后，查看了一下h1_abc_base.yaml文件，其中已经包含了pip安装的一些内容，不过为了以防万一还是也把pip的内容导出一遍，如下所示，会生成requirements.txt
```shell
(h1_abc_base) abc@h1:~$ pip freeze > h1_abc_base_requirements.txt
```


# 目标机器B的迁移

下载目标机器A最终生成的 ```h1_abc_base.yaml``` 与 ```h1_abc_base_requirements.txt```，复制到目标机器的个人conda/miniconda目录下 ```/data/sdb1/lyx/```

首先执行如下操作，导入conda环境
```shell
(base) lyx@h5:/data/sdb1/lyx$ conda env create -f h1_abc_base.yaml

...

Successfully built docopt jieba pyrouge sklearn termcolor textrank4zh
Installing collected packages: jmespath, numpy, cached-property, botocore, werkzeug, threadpoolctl, scipy, s3transfer, protobuf, markdown, joblib, h5py, grpcio, yarg, torch, termcolor, tensorboard, scikit-learn, regex, pytz, networkx, keras-preprocessing, keras-applications, jieba, gast, docopt, dill, boto3, astor, absl-py, textrank4zh, tensorflow, tensorboardx, sklearn, rouge, pytorch-pretrained-bert, pyrouge, pipreqs, pandas, multiprocess, emoji
Successfully installed absl-py-0.11.0 astor-0.8.1 boto3-1.17.68 botocore-1.20.69 cached-property-1.5.2 dill-0.3.3 docopt-0.6.2 emoji-1.2.0 gast-0.4.0 grpcio-1.33.2 h5py-3.1.0 jieba-0.42.1 jmespath-0.10.0 joblib-0.17.0 keras-applications-1.0.8 keras-preprocessing-1.1.2 markdown-3.3.3 multiprocess-0.70.11.1 networkx-2.5.1 numpy-1.16.0 pandas-1.1.4 pipreqs-0.4.10 protobuf-3.14.0 pyrouge-0.1.3 pytorch-pretrained-bert-0.6.2 pytz-2020.4 regex-2021.4.4 rouge-1.0.0 s3transfer-0.4.2 scikit-learn-0.23.2 scipy-1.5.4 sklearn-0.0 tensorboard-1.12.2 tensorboardx-2.1 tensorflow-1.12.0 termcolor-1.1.0 textrank4zh-0.3 threadpoolctl-2.1.0 torch-1.1.0 werkzeug-1.0.1 yarg-0.1.9
```

其次执行如下操作，安装pip的包的版本（不一定要做）：
```shell
(base) lyx@h5:/data/sdb1/lyx$ conda activate h1_abc_base
(h1_abc_base) lyx@h5:/data/sdb1/lyx$ pip install -r h1_abc_base_requirements.txt 
```

**注意目前的安装操作还是需要在线环境才能安装的，离线安装的时候可能需要在导出环境配置文件的同时把下载环境的那些文件也都一起导出来，未来如果有需求的时候再进一步研究了！**

```
(h1_abc_base) lyx@h5:/data/sdb1/lyx$ python
Python 3.6.12 |Anaconda, Inc.| (default, Sep  8 2020, 23:10:56) 
[GCC 7.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import torch
>>> torch.cuda.is_available()
True
```
