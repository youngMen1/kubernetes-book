# Pod

容器的一个集合，kubernetes为每个Pod都分配了唯一的IP地址，称为PodIP,一个Pod里的多个容器共享PodIP地址，kubernetes要求底层网络支持集群内任意两个Pod之间的TCP/IP直接通信，这通常采用虚拟二层网络技术实现，例如Flannel、Open vSwitch,因此在需要记住的是在kubernetes里，一个Pod里的容器与另外主机上的Pod容器能够直接通信。

pod、容器与Node的关系图

PodIP+容器端口=Endpoint\(端点，它代表着此pod里的一个服务进程的对外通信地址\)

