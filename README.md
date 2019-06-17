# 注册中心

## 流程

首先看一下dubbo经典的架构图：

![dubbo_architecture](https://github.com/dubbo-x/registry/blob/master/img/dubbo_architecture.png)

从图中可以看出dubbo的流程大致如下：

- 首先看步骤一：provider向registry注册，其中注册内容包括（但不限于）：
  - 地址和端口
  - 协议：dubbo，jsonrpc等等
  - 服务名，也可以叫做接口名
  - 该服务提供的方法
- 其次看步骤二：consumer向registry订阅，并获取provider向registry注册的内容（但不限于）：
  - 地址和端口
  - 协议
- 然后看步骤四：consumer通过拿到的内容去请求provider

## 例子

下图展示了把zookeeper作为注册中心的大致流程：

![zookeeper](https://github.com/dubbo-x/registry/blob/master/img/zookeeper.png)

其中图片出自文章：[在Dubbo应用中使用Zookeeper](http://dubbo.apache.org/zh-cn/blog/dubbo-zk.html)。

## url

在了解dubbo-go的registry之前，首先要了解dubbo-go的[url](https://github.com/apache/dubbo-go/blob/master/common/url.go)这一概念。这是因为dubbo-go的注册和订阅都是通过url来实现的：

- provider向registry注册的服务信息是以url的形式存储的
- consumer向registry订阅并获取的服务信息，也是通过url匹配到相应的，才返回的

下面看一下url的大致模样：

```go
type baseUrl struct {
	Protocol     string
	Location     string // ip+port
	Ip           string
	Port         string
	Params       url.Values
	PrimitiveURL string
	ctx          context.Context
}

type URL struct {
	baseUrl
	Path     string // like  /com.ikurento.dubbo.UserProvider3
	Username string
	Password string
	Methods  []string
	//special for registry
	SubURL *URL
}
```

其中

- Ip，Port：地址和端口
- Path：服务名
- Methods：该服务提供的方法
- Protocol：协议

## registry

dubbo-go关于注册中心的代码主要位于[registry](https://github.com/apache/dubbo-go/tree/master/registry)目录下。为了保证一定的可扩展性，dubbo-go抽象出了[Registry接口](https://github.com/apache/dubbo-go/blob/master/registry/registry.go)，代码如下：

```go
// Extension - Registry
type Registry interface {
	common.Node
	//used for service provider calling , register services to registry
	//And it is also used for service consumer calling , register services cared about ,for dubbo's admin monitoring.
	Register(url common.URL) error

	//used for service consumer ,start subscribe service event from registry
	Subscribe(common.URL) (Listener, error)
}
```

## 补充

## 术语

- dubbo：一般指java版本的微服务框架，也可特指rpc传输协议，根据语境区别
- dubbo-go：指dubbo的go版本
- provider：server端
- consumer：client端
- registry：注册中心
- register：注册服务
- subscribe：订阅获取服务
- invoke：client向server端发送请求
