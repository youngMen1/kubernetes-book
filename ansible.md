# 1.Ansible总结

Ansible是一种自动化运维工具,基于paramiko开发的,并且基于模块化工作，Ansible是一种集成IT系统的配置管理、应用部署、执行特定任务的开源平台，它是基于python语言，由Paramiko和PyYAML两个关键模块构建。集合了众多运维工具的优点，实现了批量系统配置、批量程序部署、批量运行命令等功能.ansible是基于模块工作的,本身没有批量部署的能力.真正具有批量部署的是ansible所运行的模块，ansible只是提供一种框架.ansible不需要在远程主机上安装client/agents，因为它们是基于ssh来和远程主机通讯的。

Ansible被定义为配置管理工具,配置管理工具通常具有以下功能:

* 确保所依赖的软件包已经被安装
* 配置文件包含正确的内容和正确的权限
* 相关服务被正确运行

常用的自动化运维工具技术特性比较:

| 项目 | Puppet | SaltStack | Ansible |
| :--- | :--- | :--- | :--- |
| 开发语言 | Ruby | Python | Python |
| 是否有客户端 | 有 | 有 | 无 |
| 是否支持二次开发 | 不支持 | 支持 | 支持 |
| 服务器与远程机器是否相互验证 | 是 | 是 | 是 |
| 服务器与远程机器的通信是否加密 | 是，标准的SSL协议 | 是，使用AES加密 | 是，使用OpenSSH |
| 平台支持 | AIX , BSD, HP-UX, Linux , Mac OSX , Solaris, Windows | BSD, Linux , Mac OS X , Solaris, Windows | AIX , BSD , HP-UX , Linux , Mac OS X , Solaris |
| 是否提供Web UI | 提供 | 提供 | 提供，但是是商业版本 |
| 配置文件格式 | Ruby 语法格式 | YAML | YAML |
| 命令行执行 | 不支持，大师可以通过配置模块实现 | 支持 | 支持 |

## 1.1.Ansible基本架构

ansible系统由控制主机和被管理主机组成,控制主机不支持windows平台

![](/static/image/6078939-1799907d732a3e87.webp)

* 核心: ansible
* Core Modules: ansible自带的模块
* Custom Modules: 核心模块功能不足时,用户可以添加扩展模块
* Plugins: 通过插件来实现记录日志,发送邮件或其他功能
* Playbooks: 剧本,YAML格式文件，多个任务定义在一个文件中，定义主机需要调用哪些模块来完成的功能
* Connectior Plugins: ansible基于连接插件连接到各个主机上,默认是使用ssh
* Host Inventory: 记录由Ansible管理的主机信息，包括端口、密码、ip等

## 1.2.Ansible特点

部署简单, 只需要在控制主机上部署ansible环境,被控制端上只要求安装ssh和python 2.5以上版本,这个对于类unix系统来说相当与无需配置.

* no angents: 被管控节点无需安装agent
* no server: 无服务端,使用是直接调用命名
* modules in any languages: 基于模块工作, 可以使用任意语言开发模块
* 易读的语法: 基于yaml语法编写playbook
* 基于推送模式: 不同于puppet的拉取模式,直接由调用者控制变更在服务器上发生的时间
* 模块是幂等性的:定义的任务已存在则不会做任何事情,意味着在同一台服务器上多次执行同一个playbook是安全的

## 1.3.Ansible程序目录结构

* 配置文件: /etc/ansible/
* 执行文件目录: /usr/bin/
* lib依赖库: /usr/lib/python2.7/site-packages/ansible/
* help文件: /usr/lib/python2.7/site-packages/ansible

# 2.Ansible任务执行

## 2.1.Ansible任务执行模式

Ansible任务执行模式分为以下两种:

* ad-hoc模式(点对点模块)

使用单个模块,支持批量执行单条命令,相当与在bash中执行一句shell命令

* playbook模式(剧本模式)

ansible主要的管理方式,通过多个task的集合完成一类功能,可以理解为多个ad-hoc的配置文件


## 2.2.Ansible执行流程

![](/static/image/6078939-2d6e5a817186480d.webp)

# 3.Ansible使用实例

## 3.1.安装
1.编译安装
ansible依赖于Python 2.6或更高的版本、paramiko、PyYAML及Jinja2。


```
# yum -y install python-jinja2 PyYAML python-paramiko python-babel python-crypto
# tar xf ansible-1.5.4.tar.gz
# cd ansible-1.5.4
# python setup.py build
# python setup.py install
# mkdir /etc/ansible
# cp -r examples/* /etc/ansible
```

2.rpm包安装

```
# yum install ansible
```

## 3.2.修改配置文件

ansible配置文件查找顺序

1.检查环境变量ANSIBLE_CONFIG指向的路径文件(`export ANSIBLE_CONFIG=/etc/ansible.cfg`)；
2.`~/.ansible.cfg`，检查当前目录下的ansible.cfg配置文件；
3.`/etc/ansible.cfg`检查etc目录的配置文件。

ansible配置文件
ansible 有许多参数，下面我们列出一些常见的参数：


```
    inventory = /etc/ansible/hosts      #这个参数表示资源清单inventory文件的位置
    library = /usr/share/ansible        #指向存放Ansible模块的目录，支持多个目录方式，只要用冒号（：）隔开就可以
    forks = 5       #并发连接数，默认为5
    sudo_user = root        #设置默认执行命令的用户
    remote_port = 22        #指定连接被管节点的管理端口，默认为22端口，建议修改，能够更加安全
    host_key_checking = False       #设置是否检查SSH主机的密钥，值为True/False。关闭后第一次连接不会提示配置实例
    timeout = 60        #设置SSH连接的超时时间，单位为秒
    log_path = /var/log/ansible.log     #指定一个存储ansible日志的文件（默认不记录日志）
    
```

## 3.3.定义Inventory(主机列表)

ansible的主要功用在于批量主机操作，为了便捷地使用其中的部分主机，可以在inventory file中将其分组命名。默认的inventory file为/etc/ansible/hosts。
inventory file可以有多个，且也可以通过Dynamic Inventory来动态生成。




# 4.Ansible速度优化

# 5.Ansible与docker

ansible内置多种云计算相关模块,如aws,openstack,docker等,下图是ansible与docker相关的模块:

![](/static/image/6078939-dd7555be152cfb66.webp)

## 5.1.使用ansible创建docker镜像

通过playbook和dockerfile相结合的方式生成镜像, 示例如下:

```
FROM ansible/ubuntu14.04-ansible:stable
MAINTAINER xxx

ADD ansible /srv/ansible
WORKDIR /srv/ansible

RUN ansible-playbook web-app.yaml -c local

VOLUME /srv/project/static

WORKDIR /srv/project

EXPOSE 8000

CMD ["gunicorn_django", "-c", "gunicorn.conf.py"]
```
## 5.2.启动容器

ansible可以通过docker模块来操作容器,示例如下:


```
- name: start the postgres container
  docker:
    image: postgres:9.4
    name: postgres
    public_all_ports: True
    env:
        POSTGRES_USER: "{{ database_user }}"
        POSTFRES_PASSWORD: "{{ database_password }}"
```



# 6.参考

Ansible总结：[https://www.jianshu.com/p/c82737b5485c](https://www.jianshu.com/p/c82737b5485c)

