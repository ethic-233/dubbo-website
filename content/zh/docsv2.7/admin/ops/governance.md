---
type: docs
title: "服务治理和配置管理"
linkTitle: "服务治理和配置管理"
weight: 4
---

## 服务治理

服务治理主要作用是改变运行时服务的行为和选址逻辑，达到限流，权重配置等目的，主要有以下几个功能：  

### 应用级别的服务治理

在 Dubbo 2.6 及更早版本中，所有的服务治理规则都只针对服务粒度，如果要把某条规则作用到应用粒度上，需要为应用下的所有服务配合相同的规则，变更，删除的时候也需要对应的操作，这样的操作很不友好，因此 Dubbo 2.7 版本中增加了应用粒度的服务治理操作，对于条件路由(包括黑白名单)，动态配置(包括权重，负载均衡)都可以做应用级别的配置：  

![condition](/imgs/blog/admin/conditionRoute.jpg)  
 
上图是条件路由的配置，可以按照应用名，服务名两个维度来填写，也可以按照这两个维度来查询。  

### 标签路由  

标签路由是 Dubbo 2.7 引入的新功能，配置以应用作为维度，给不同的服务器打上不同名字的标签，配置如下图所示：

![tag](/imgs/blog/admin/route.jpg)

调用的时候，客户端可以通过`setAttachment`的方式，来设置不同的标签名称，比如本例中，`setAttachment(tag1)`，客户端的选址范围就在如图所示的三台机器中，可以通过这种方式来实现流量隔离，灰度发布等功能。

### 条件路由

条件路由是 Dubbo 一直以来就有的功能，目前可以配置服务和应用两个维度，条件路由为 `yaml` 格式，具体的规则体以及各种适用场景，请参考[这里](../../../user/examples/routing-rule)

### 黑白名单  

黑白名单是条件路由的一部分，规则存储和条件路由放在一起，为了方便配置所以单独拿出来，同样可以通过服务和应用两个维度，指定黑名单和白名单:  

![blackList](/imgs/admin/blackList.jpg) 

### 动态配置  
动态配置是和路由规则平行的另一类服务治理治理功能，主要作用是在不重启服务的情况下，动态改变调用行为，从Dubbo2.7版本开始，支持服务和应用两个维度的配置，采用`yaml`格式，界面如下： 
 
![config](/imgs/admin/config.jpg)

具体的规则体说明请参考[这里](../../../user/examples/config-rule)

### 权重调节  
权重调节是动态配置的子功能，主要作用是改变服务端的权重，更大的权重会有更大的几率被客户端选中作为服务提供者，从而达到流量分配的目的：  

![weight](/imgs/admin/weight.jpg)

### 负载均衡
负载均衡也是动态配置的子功能，主要作用是调整客户端的选址逻辑，目前可选的负载均衡策略有随机，轮训和最小活跃，关于各个策略的解释请参考[这里](../../../user/examples/loadbalance)

## 配置管理
配置管理也是配合Dubbo2.7新增的功能，在Dubbo2.7中，增加了全局和应用维度的配置，分别在全局和应用范围内生效，其中应用配置也可以指定该应用中的服务级别的配置，可以在控制台中查看，修改配置规则，默认展示全局维度的配置。

* 全局配置： 

    ![config](/imgs/blog/admin/config.jpg)  

    全局配置里可以指定注册中心，元数据中心的地址，服务端和客户端的超时时间等，这些配置在全局内生效。除了配置写入，也可以用来查看。如果使用zookeeper作为注册中心和元数据中心，还可以看到配置文件所在位置的目录结构。  
    
* 应用， 服务配置  

    ![appConfig](/imgs/blog/admin/appConfig.jpg)  
    
    应用级别的配置可以为应用或者应用内的服务指定配置，在服务维度上，需要区分提供者和消费者。`dubbo.reference.{serviceName}`表示作为该服务消费者的配置，`dubbo.provider.{servcieName}`表示作为该服务提供者的配置。其中注册中心和元数据中心的地址，只能在全局配置中指定，这也是Dubbo2.7中推荐的使用方式。  
    
* 优先级： 服务配置 > 应用配置 > 全局配置