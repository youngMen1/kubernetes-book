# 1.Ansible

## 1.1.Ansible介绍

ansible是一种自动化运维工具,基于paramiko开发的,并且基于模块化工作，Ansible是一种集成IT系统的配置管理、应用部署、执行特定任务的开源平台，它是基于python语言，由Paramiko和PyYAML两个关键模块构建。集合了众多运维工具的优点，实现了批量系统配置、批量程序部署、批量运行命令等功能.ansible是基于模块工作的,本身没有批量部署的能力.真正具有批量部署的是ansible所运行的模块，ansible只是提供一种框架.ansible不需要在远程主机上安装client/agents，因为它们是基于ssh来和远程主机通讯的

Ansible被定义为配置管理工具,配置管理工具通常具有以下功能:

* 确保所依赖的软件包已经被安装
* 配置文件包含正确的内容和正确的权限
* 相关服务被正确运行

常用的自动化运维工具技术特性比较:

| 项目 | Puppet | SaltStack | Ansible |
| :--- | :--- | :--- | :--- |


| 开发语言 | Ruby | Python | Python |
| :--- | :--- | :--- | :--- |


| 是否有客户端 | 有 | 有 | 无 |
| :--- | :--- | :--- | :--- |


| 是否支持二次开发 | 不支持 | 支持 | 支持 |
| :--- | :--- | :--- | :--- |


| 服务器与远程机器是否相互验证 | 是 | 是 | 是 |
| :--- | :--- | :--- | :--- |


| 服务器与远程机器的通信是否加密 | 是，标准的SSL协议 | 是，使用AES加密 | 是，使用OpenSSH |
| :--- | :--- | :--- | :--- |


| 平台支持 | AIX , BSD, HP-UX, Linux , Mac OSX , Solaris, Windows | BSD, Linux , Mac OS X , Solaris, Windows | AIX , BSD , HP-UX , Linux , Mac OS X , Solaris |
| :--- | :--- | :--- | :--- |


| 是否提供Web UI | 提供 | 提供 | 提供，但是是商业版本 |
| :--- | :--- | :--- | :--- |


| 配置文件格式 | Ruby 语法格式 | YAML | YAML |
| :--- | :--- | :--- | :--- |


| 命令行执行 | 不支持，大师可以通过配置模块实现 | 支持 | 支持 |
| :--- | :--- | :--- | :--- |


## 1.2.Ansible基本架构

Ansible系统由控制主机和被管理主机组成,控制主机不支持windows平台

![](/static/image/6078939-1799907d732a3e87.webp)

* 核心: ansible
* Core Modules: ansible自带的模块
* Custom Modules: 核心模块功能不足时,用户可以添加扩展模块
* Plugins: 通过插件来实现记录日志,发送邮件或其他功能
* Playbooks: 剧本,YAML格式文件，多个任务定义在一个文件中，定义主机需要调用哪些模块来完成的功能
* Connectior Plugins: ansible基于连接插件连接到各个主机上,默认是使用ssh
* Host Inventory: 记录由Ansible管理的主机信息，包括端口、密码、ip等

## 1.3.Ansible特点

部署简单, 只需要在控制主机上部署ansible环境,被控制端上只要求安装ssh和python 2.5以上版本,这个对于类unix系统来说相当与无需配置

1.no angents: 被管控节点无需安装agent
2.no server: 无服务端,使用是直接调用命名
3.modules in any languages: 基于模块工作, 可以使用任意语言开发模块
4.易读的语法: 基于yaml语法编写playbook
5.基于推送模式: 不同于puppet的拉取模式,直接由调用者控制变更在服务器上发生的时间
6.模块是幂等性的:定义的任务已存在则不会做任何事情,意味着在同一台服务器上多次执行同一个playbook是安全的

## 1.4.Ansible程序目录结构

* 配置文件: **/etc/ansible/**
* 执行文件目录: **/usr/bin/**
* lib依赖库: **/usr/lib/python2.7/site-packages/ansible/**
* help文件: **/usr/lib/python2.7/site-packages/ansible**

作者：drfung
链接：https://www.jianshu.com/p/c82737b5485c
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。














# 3.参考

Ansible总结：[https://www.jianshu.com/p/c82737b5485c](https://www.jianshu.com/p/c82737b5485c)

