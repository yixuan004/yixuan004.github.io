---
title: SpringBoot-11基于ThriftRPC调用Python的模型服务+设计模式
date: 2023-06-09 00:37:20
tags:
	- springboot
categories:
	- develop
	- java
	- springboot
---

# SpringBoot11-基于ThriftRPC调用Python的模型服务+设计模式

首先RPC在开发这边是一个很重点的内容，目前浅显的理解来说，RPC相比HTTP服务的区别在于RPC有自己的协议栈，而且不会像外部暴露端口，一般是一个系统内部调用的时候使用RPC的方式，而且应该来说是有更快速的通信速度

本文使用Thrift这一款RPC框架，RPC有许多种框架，其中Thrift是比较常用的一种框架爱

而设计模式也是java开发和所有开发中的一种重点内容，这里的如果要使用Java调用Python的模型服务的话，不可能让ThriftClinet每次都新建连接，这样的开销实在太大了，所以应该是先建立好连接后，一直复用这个连接，这也引出了设计模式的概念

## 1. Thrift的安装

对于Thrift安装，可以直接使用brew安装，但是要注意一下安装版本的问题，因为使用Java的JDK版本还有SpringBoot版本之间的匹配，这里Thrift最终选用了0.9.3这个版本。arm架构下homebrew安装请参考这篇博客：https://yixuan004.github.io/2023/06/07/mixed/linuxmacos/macOS%E5%8F%8ALinux-%E5%9C%A8arm%E6%9E%B6%E6%9E%84mac%E8%8A%AF%E7%89%87%E4%B8%8A%E5%AE%89%E8%A3%85homebrew/

```shell
brew install thrift@0.9
```

安装后，可能会有如下提示，需要添加一些环境变量的内容

```
thrift@0.9 is keg-only, which means it was not symlinked into /usr/local,
because this is an alternate version of another formula.

If you need to have thrift@0.9 first in your PATH, run:
  echo 'export PATH="/usr/local/opt/thrift@0.9/bin:$PATH"' >> ~/.zshrc

For compilers to find thrift@0.9 you may need to set:
  export LDFLAGS="-L/usr/local/opt/thrift@0.9/lib"
  export CPPFLAGS="-I/usr/local/opt/thrift@0.9/include"

For pkg-config to find thrift@0.9 you may need to set:
  export PKG_CONFIG_PATH="/usr/local/opt/thrift@0.9/lib/pkgconfig"
```

查看安装后的版本

```shell
(base) ➜ /Users/curious ✻ thrift -version                            [22:11:48]
Thrift version 0.9.3
```

## 2. 对Java/Python一个基础”代码“的初始化

需要首先照猫画虎编写一个`ClassifyService.thrift`，简单描述一下这个文件，就是定义了一个java和一个python的namespace，表示生成的文件应该放在什么位置下。然后定一个两个方法的接口，一个是ping就是心跳检测这样的，另外一个就是用来进行文本分类的（这里简化了一下，就只有一个name作为输入，实际上还应该带上模型版本号什么的）

```
namespace java cn.edu.bupt.aiswitchboard.thrift
namespace py aiswitchboard.thrift

service ClassifyService {
  bool ping(),
  i32 classify(1: string name)
}
```

这个初始化的代码可以放在SpringBoot项目的这个路径下，方便之后推git

```shell
aiswitchboard/src/thrift/ClassifyService.thrift
```

生成这个基础代码后，使用brew安装的thrift进行代码的生成

```shell
thrift --gen py ClassifyService.thrift
thrift --gen java ClassifyService.thrift
```

会在目录下生成java和python的代码，目录结构如下

```shell
.
├── __init__.py
└── aiswitchboard
    ├── __init__.py
    ├── __pycache__
    │   └── __init__.cpython-36.pyc
    └── thrift
        ├── ClassifyService-remote
        ├── ClassifyService.py
        ├── __init__.py
        ├── __pycache__
        │   ├── ClassifyService.cpython-36.pyc
        │   ├── __init__.cpython-36.pyc
        │   └── ttypes.cpython-36.pyc
        ├── constants.py
        └── ttypes.py

└── cn
    └── edu
        └── bupt
            └── aiswitchboard
                └── thrift
                    └── ClassifyService.java
```

## 3. 启动 + 整合进入SpringBoot项目中

### 3.1 Python启动

启动python这里的代码，我这里thrift版本使用的是0.9.3的版本，可能python会报错一个找不到ttypes的错误，开始还以为ttypes是一个外面的库，后来发现是自己这里的，应该是有一个import改成import .ttypes就可以了

python的启动还需要自己写一个ServiceHandler，然后把生成py的目录给append进环境里面来

这里踩了一个坑是127.0.0.1和localhost的区别，可能一些场景下还是尽量避免DNS解析，写127.0.0.1比localhost好吧

```python
import sys
sys.path.append('/Users/curious/Desktop/thriftinstall/gen_py')

from thrift.transport import TSocket
from thrift.transport import TTransport
from thrift.protocol import TBinaryProtocol
from thrift.server import TServer

from aiswitchboard.thrift import ClassifyService

class ClassifyServiceHandler:
    def ping(self):
        return True

    def classify(self, content):
        return 1

if __name__ == '__main__':
    handler = ClassifyServiceHandler()
    processor = ClassifyService.Processor(handler)
    
    transport = TSocket.TServerSocket('127.0.0.1', 9744)
    tfactory = TTransport.TBufferedTransportFactory()
    pfactory = TBinaryProtocol.TBinaryProtocolFactory()

    server = TServer.TSimpleServer(processor, transport, tfactory, pfactory)

    print("Starting the Python server...")
    server.serve()
    print("Python server stopped.")
```

### 3.2 整合进入SpringBoot项目中

生成的ClassifyService类可以放到thrift目录下，这个thrift目录和其他java目录是平级的，比如controller，service那些的

```shell
aiswitchboard/src/main/java/cn/edu/bupt/aiswitchboard/thrift/ClassifyService.java
```

然后编写一个ThriftClient类，放在和controller service那些相同的目录下

`aiswitchboard/src/main/java/cn/edu/bupt/aiswitchboard/client/ThriftClient.java`

```java
package cn.edu.bupt.aiswitchboard.client;
import cn.edu.bupt.aiswitchboard.thrift.ClassifyService;
import org.apache.thrift.protocol.TBinaryProtocol;
import org.apache.thrift.protocol.TProtocol;
import org.apache.thrift.transport.TSocket;
import org.apache.thrift.transport.TTransport;
import org.springframework.stereotype.Component;

@Component
public class ThriftClient {
    private String host;
    private Integer port;  // 端口

    // 单例模式
    public ThriftClient() {
    }

    public ThriftClient(String host, Integer port) {
        this.host = host;
        this.port = port;
    }

    public void callPythonService(String sceneName, String text) {
        try {
            TTransport transport = new TSocket(host, port);
            transport.open();

            TProtocol protocol = new TBinaryProtocol(transport);
            ClassifyService.Client client = new ClassifyService.Client(protocol);

            // 调用Python服务的方法
            boolean pingResult = client.ping();
            System.out.println("Ping result: " + pingResult);

            int classifyResult = client.classify(text);
            System.out.println("classifyResult result: " + classifyResult);

            transport.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

由于需要反复使用这个ThriftClient，故使用单例模式进行初始化，避免反复初始化，这个写在的位置是utils文件夹里面

`aiswitchboard/src/main/java/cn/edu/bupt/aiswitchboard/utils/ThriftClientSingleton.java`

```java
package cn.edu.bupt.aiswitchboard.utils;

import cn.edu.bupt.aiswitchboard.client.ThriftClient;

public class ThriftClientSingleton {
    private static ThriftClient instance;

    private ThriftClientSingleton() {
        instance = new ThriftClient("127.0.0.1", 9744);
    }

    public static ThriftClient getInstance() {
        if (instance == null) {
            synchronized (ThriftClientSingleton.class) {
                if (instance == null) {
                    new ThriftClientSingleton();
                }
            }
        }
        return instance;
    }

}
```

### 3.3 在SpringBoot项目中调用

单例模式

```java
ThriftClientSingleton.getInstance().callPythonService(sceneName, text);
```







