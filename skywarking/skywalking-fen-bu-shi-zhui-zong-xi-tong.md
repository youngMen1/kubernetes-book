# 1.SkyWalking 分布式追踪系统

## 1.1.SkyWalking简介

```
SkyWalking是一个开源的观测平台，用于从服务和云原生等基础设施中收集、分析、聚合以及可视化数据。SkyWalking 提供了一种简便的方式来清晰地观测分布式系统，甚至可以观测横跨不同云的系统。SkyWalking 更像是一种现代的应用程序性能监控（Application Performance Monitoring，即APM）工具，专为云原生，基于容器以及分布式系统而设计
```

SkyWalking 在逻辑上分为四部分：**探针、平台后端、存储和用户界面**。其架构图如下：  
![](/static/image/19037705-c6cd5fe0547e57a1.webp)

* 探针：基于不同的来源探针可能是不一样的，但作用都是收集数据，将数据格式化为 SkyWalking 适用的格式。例如在Java中则是做字节码植入，无侵入式的收集，并通过 HTTP 或者 gRPC 方式发送数据到平台后端

* 平台后端：是一个支持集群模式运行的后台，用于数据聚合、数据分析以及驱动数据流从探针到用户界面的流程。平台后端还提供了各种可插拔的能力，如不同来源数据（如来自 Zipkin）格式化，不同存储系统以及集群管理。你甚至还可以使用观测分析语言来进行自定义聚合分析。

* 存储：是开放式的，可以选择一个既有的存储系统，如 ElasticSearch、H2 或 MySQL 集群（Sharding-Sphere 管理），也可以选择自己实现一个存储系统。

* 用户界面：也就是SkyWalking的可视化界面，UI非常炫酷且强大，同样它也是可定制以匹配你已存在的后端的

SkyWalking 为观察和监控分布式系统提供了许多不同场景下的解决方案。例如为Java、C\#及Node.js提供语言自动探针，无侵入式的收集。同时也为一些编译型语言C++、GO等提供了手动打点 SDK（目前还未支持）。除此之外，还可以使用服务网格基础探针来收集数据，以帮助了解整个分布式系统。

在SkyWalking中也存在服务、服务实例及端点概念，因为SkyWalking就是提供了这些概念的观测能力：

* 服务（Service）：表示对请求提供相同行为的一系列或一组工作负载。在使用打点代理或 SDK 的时候，你可以定义服务的名字。如果不定义的话，SkyWalking 将会使用你在平台上定义的名字，如 Istio。

* 服务实例（Service Instance）：上述的一组工作负载中的每一个工作负载称为一个实例。就像 Kubernetes 中的 pods 一样，服务实例未必就是操作系统上的一个进程。但当你在使用打点代理的时候， 一个服务实例实际就是操作系统上的一个真实进程。

* 端点（Endpoint）：对于特定服务所接收的请求路径，如 HTTP 的 URI 路径和 gRPC 服务的类名 + 方法签名

综上，SkyWalking 优势如下：

* 多种监控手段，语言探针和服务网格（Service Mesh）
* 模块化，UI、存储、集群管理多种机制可选
* 支持告警
* 优秀的可视化方案

更多内容可以参考官方文档：

* [SkyWalking 文档中文版（社区提供）](https://links.jianshu.com/go?to=https%3A%2F%2Fskyapm.github.io%2Fdocument-cn-translation-of-skywalking%2F)

* [Apache SkyWalking 官方文档](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fapache%2Fskywalking%2Ftree%2Fmaster%2Fdocs)

## 1.2.Linux环境搭建SkyWalking服务

对SkyWalking有一个大致的了解后，本小节我们来在CentOS7上搭建 SkyWalking 服务。首先我们需要获取到SkyWalking的下载地址，官方下载地址如下：

[http://skywalking.apache.org/downloads/](https://links.jianshu.com/go?to=http%3A%2F%2Fskywalking.apache.org%2Fdownloads%2F)

这里我选择当前最新的6.6.0版本的二进制包：

![](/static/image/19037705-3ca72e9a4a2d7408.webp)

复制下载地址到服务器上进行下载并解压，具体步骤如下：


```
[root@localhost ~]# cd /usr/local/src
[root@localhost /usr/local/src]# wget http://mirrors.tuna.tsinghua.edu.cn/apache/skywalking/6.6.0/apache-skywalking-apm-6.6.0.tar.gz
[root@localhost /usr/local/src]# mkdir ../skywalking && tar -zxvf apache-skywalking-apm-6.6.0.tar.gz -C ../skywalking --strip-components 1
[root@localhost /usr/local/src]# cd ../skywalking/
[root@localhost /usr/local/skywalking]# ll -rh  # 解压后的目录文件如下
total 88K
drwxr-xr-x 2 root root   53 Dec 28 18:22 webapp
-rw-rw-r-- 1 1001 1002 2.0K Dec 24 14:10 README.txt
drwxrwxr-x 2 1001 1002  12K Dec 24 14:28 oap-libs
-rwxrwxr-x 1 1001 1002  32K Dec 24 14:10 NOTICE
drwxrwxr-x 3 1001 1002 4.0K Dec 28 18:22 licenses
-rwxrwxr-x 1 1001 1002  29K Dec 24 14:10 LICENSE
drwxr-xr-x 2 root root  221 Dec 28 18:22 config
drwxr-xr-x 2 root root  241 Dec 28 18:22 bin
drwxrwxr-x 8 1001 1002  143 Dec 24 14:21 agent
[root@localhost /usr/local/skywalking]# 
```

运行bin目录下的**startup.sh**脚本即可启动skywalking服务：


```
[root@localhost /usr/local/skywalking]# bin/startup.sh
SkyWalking OAP started successfully!
SkyWalking Web Application started successfully!
[root@localhost /usr/local/skywalking]#
```

SkyWalking控制台服务默认监听8080端口，若有防火墙需要开放该端口：


```
[root@localhost /usr/local/skywalking]# firewall-cmd --zone=public --add-port=8080/tcp --permanent
success
[root@localhost /usr/local/skywalking]# firewall-cmd --reload
success
[root@localhost /usr/local/skywalking]#
```

若希望允许远程传输，则还需要开放11800（gRPC）和12800（rest）端口，远程agent将通过该端口传输收集的数据：


```
[root@localhost /usr/local/skywalking]# firewall-cmd --zone=public --add-port=11800/tcp --permanent
success
[root@localhost /usr/local/skywalking]# firewall-cmd --zone=public --add-port=12800/tcp --permanent
success
[root@localhost /usr/local/skywalking]# firewall-cmd --reload
success
[root@localhost /usr/local/skywalking]#
```



