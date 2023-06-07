---
title: iterm2+ohmyzsh安装及插件配置
date: 2021-12-06 12:04:41
tags: 
    - linuxmacos
categories:
	- develop
	- mixed
	- linuxmacos
---

背景：iterm2可以替代macOS自带的terminal，而ohmyzsh是基于zsh命令行的一个扩展工具集，在其上可以安装插件来丰富命令行的功能（何老板推荐的syntax highlighting和auto suggestion）

<!--more-->

# 安装item2

直接官网安装即可，安装后把其保留在程序坞中，手工替代terminal
> https://iterm2.com/


# 安装ohmyzsh

直接执行官网的一句话命令安装：
```shell
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
> https://ohmyz.sh/#install



# 安装ohmyzsh的插件syntax highlighting和auto suggestion

Reference:
> https://www.jianshu.com/p/2f2c09ebfb7f
> https://blog.csdn.net/qq_38839744/article/details/90247628

## zsh-syntax-highlighting安装

官网：
> https://github.com/zsh-users/zsh-syntax-highlighting


安装：
```shell
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git 
echo "source ${(q-)PWD}/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ${ZDOTDIR:-$HOME}/.zshrc
```

生效：
```shell
source ~/.zshrc
```


## Auto suggestion的安装

auto suggestion是ohmyzsh的一个插件，作用基本上是根据历史输入指令记录即使的提示，能够提高效率

1. git clone项目，并与ZSH建立关联

```
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

其中通过echo查看$ZSH_CUSTOM

```shell
➜  ~ echo $ZSH_CUSTOM
/Users/curious/.oh-my-zsh/custom
```


2. 编辑~/.zshrc文件

```shell
vim ~/.zshrc
```

按i 进入–INSERT–编辑模式，使用上下左右键找找到plugins=(git)这一行添加

```shell
plugins=(git zsh-autosuggestions)
```





