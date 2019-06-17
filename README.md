# 注册中心

## 流程

首先看一下dubbo经典的架构图：


从图中可以看出dubbo的大致流程如下：

- 首先，provider（也可以理解为server端）向注册中心registry注册（register），其中注册内容包括（但不限于）：
  - 服务名，也可以叫做接口名
  - 该服务提供的方法名
  - 地址和端口
  - 协议：dubbo（这里特指dubbo的rpc协议，而不是微服务框架），jsonrpc等等
- 其次，consumer（也可以理解为client端）向注册中心registry订阅（subscribe），获取provider向registry注册的内容（但不限于）：
  - 地址和端口
  - 协议
- 然后，consumer通过拿到的地址和端口，并使用对应的协议去请求provider

## 例子

下面以zookeeper注册中心为例：

## dubbo-go

https://github.com/apache/dubbo-go/blob/master/common/url.go#L74-L82