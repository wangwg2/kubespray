## Kubespray 
###### Online Docs
* [kubernetes-incubator/kubespray](https://github.com/kubernetes-incubator/kubespray)
* [junzikai/kubespray_1.9.0_Ali](https://github.com/junzikai/kubespray_1.9.0_Ali)
* [kubernetes 1.8.4国内安装方案(kubespray)](https://www.jianshu.com/p/f61700d1878f)
* [使用 Kubespray 在基础设施或云平台上安装 Kubernetes](https://k8smeetup.github.io/docs/getting-started-guides/kubespray/)
* [使用kuberspay无坑安装生产级Kubernetes集群](http://www.wisely.top/2017/07/01/no-problem-kubernetes-kuberspay/)
* [kubenetes-dashboard安装](http://www.wisely.top/2017/07/04/kubespray-kubernetes-dashboard/)
* [如何更“优雅”地部署Kubernetes集群](https://juejin.im/entry/5a03f98d6fb9a04524054516)
* [Kubespray 集群安裝](https://feisky.gitbooks.io/kubernetes/deploy/kubespray.html)
* [Kubernetes中文社区](https://www.kubernetes.org.cn/)
* [Installing Kubernetes On-premises/Cloud Providers with Kubespray](https://kubernetes.io/docs/getting-started-guides/kubespray/)


----
修改kubespray代码

```
kubespray/roles/kubernetes-apps/ansible/defaults/main.yml
kubespray/roles/download/defaults/main.yml
kubespray/extra_playbooks/roles/download/defaults/main.yml (与上一个为同一文件)
kubespray/inventory/group_vars/k8s-cluster.yml
kubespray/roles/dnsmasq/templates/dnsmasq-autoscaler.yml
```

符号连接
```
extra_playbooks/roles 		--> roles
extra_playbooks/inventory 	--> inventory
contrib/network-storage/glusterfs/group_vars	--> inventory/group_vars
contrib/network-storage/glusterfs/roles/bootstrap-os --> roles/bootstrap-os
contrib/terraform/group_vars	--> 	inventory/group_vars
contrib/terraform/openstack/group_vars	--> inventory/group_vars
contrib/terraform/openstack/hosts  -->  contrib/terraform/openstack/terraform.py
```



----
部署生产就绪的kubernetes群集
* 可以部署在AWS，GCE，Azure，OpenStack或Baremetal上
* 高可用性群集
* 可组合（例如网络插件的选择）
* 支持最流行的Linux发行版
* 持续集成测试

要部署群集，您可以使用：
* kubespray-cli 
* Ansible usual commands and inventory builder 
* vagrant by simply running vagrant up (for tests purposes) 

支持的Linux发行版
* CoreOS 的 容器Linux
* Debian Jessie
* Ubuntu 16.04
* CentOS / RHEL 7

受支持组件的版本
* kubernetes v1.9.1 
* etcd v3.2.4 
* flanneld v0.8.0 
* calico v2.5.0 
* canal (given calico/flannel versions) 
* contiv v1.0.3 
* weave v2.0.1 
* docker v1.13 (see note)
* rkt v1.21.0 (see Note 2)

###### Requirements
* Ansible v2.4（或更新）和`python-netaddr`安装在将运行Ansible命令的机器上
* Jinja 2.9（或更新版本）需要运行Ansible Playbooks
* 目标服务器必须能够访问互联网才能拖动 docker 镜像。
* 目标服务器配置为允许IPv4转发。
* 您的ssh密钥必须复制到您的库存中的所有服务器部分。
* 该防火墙没有管理，就需要实现自己的规则，你习惯的方式。为了避免在部署期间出现任何问题，您应该禁用防火墙。

###### Network plugins
您可以选择4个网络插件。（默认：calico除了Vagrant使用flannel）
* flannel：gre / vxlan（layer 2）网络。
* calico：bgp（layer 3）网络。
* canal：calico 和 flannel 的组合。
* contiv：支持vlan，vxlan，bgp和Cisco SDN网络。这个插件能够应用防火墙策略，将容器分隔在多个网络中，并将Pod连接到物理网络上。
* weave：Weave 是一个轻量级的容器覆盖网络，不需要外部 K/V 数据库集群。

选择是用变量定义的 `kube_network_plugin`。也可以选择利用内置的云提供商网络。
网络检查： `curl http://localhost:31081/api/v1/connectivity_check`


```
Kubernetes v1.7.3
Etcd v3.2.4
Flannel v0.8.0
Docker v17.04.0-ce
```

###### 节点资讯
```
IP Address      Role              CPU   Memory
192.168.121.179	master1 + deploy  2     4G
192.168.121.106 node1             2     4G
192.168.121.197 node2             2     4G
192.168.121.123 node3             2     4G
```

###### Prepare
所有节点的网路之间可以互相通信。
部署节点(这边为 master1)对其他节点不需要 SSH 密码即可登入。
所有节点都拥有 Sudoer 权限，并且不需要输入密码。
所有节点需要安装 Python。
所有节点需要设定/etc/hosts解析到所有主机。
修改所有节点的/etc/resolv.conf


---
### Installing Kubernetes On-premises/Cloud Providers with Kubespray
通过 Kubespray 安装 Kubernetes 集群到托管在GCE、Azure、OpenStack、AWS 或 裸设备的主机上。

Kubespray是由Ansible系列 palybook、inventory、配置工具和通用 OS/Kubernetes 集群配置管理任务的领域知识组成。Kubespray提供：
* 一个高度可用的群集
* 可组合属性
* 支持大多数流行的Linux发行版
* 持续集成测试

##### Creating a cluster
###### 1.符合底层要求
配置服务器满足以下要求：
* Ansible v2.3 (or newer)
* Jinja 2.9 (or newer)
* python-netaddr installed on the machine that running Ansible commands
* 目标服务器必须能够访问互联网才能拖动 docker 镜像
* 目标服务器配置为允许IPv4转发
* 目标服务器通过SSH连接（tcp/22）直接连接到您的节点或通过堡垒主机/ssh跳转框连接
* 目标服务器拥有特权用户
* 您的SSH密钥必须复制到您的库存中的所有服务器
* 防火墙规则配置正确，以允许Ansible和Kubernetes组件进行通信
* 如果使用云提供程序，则必须具有适当的凭据并将其导出为环境变量

###### 2.编写 inventory 文件
在您配置服务器之后，为Ansible创建一个 inventory 文件。您可以手动做或通过动态库存脚本执行此操作。

###### 3.计划您的集群部署
Kubespray提供了自定义部署的许多方面的能力：
* CNI（网络）插件
* DNS配置
* 控制平面的选择：本地/二进制或dockers/rkt容器化）
* 组件版本
* Calico路由反射
* 组件运行时选项
* 证书生成方法

Kubespray自定义可以被制作成一个变量文件。如果您刚刚开始使用Kubespray，请考虑使用Kubespray默认设置来部署群集并探索Kubernetes。

###### 4.部署集群
接下来，使用以下两种方法之一来部署您的群集：
* ansible-playbook。
* kubespray-cli工具

这两种方法都运行默认的集群定义文件(`cluster.yml`) 。
大型部署（100多个节点）可能需要特定的调整以获得最佳结果。

###### 5.验证部署
Kubespray提供了一种使用Netchecker验证域间连接和DNS解析的方法。Netchecker确保netchecker-agents pod可以解析DNS请求，并在默认名称空间内ping每一个。这些吊舱模拟其余工作负载的类似行为，并作为集群健康指示器。
```
curl http://localhost:31081/api/v1/connectivity_check
```

##### Cluster operations
Kubespray提供额外的剧本来管理您的群集：规模和升级。

可以通过运行缩放手册来缩放您的群集。参考 [Adding nodes](https://github.com/kubernetes-incubator/kubespray/blob/master/docs/getting-started.md#adding-nodes).
```
ansible-playbook -i my_inventory/inventory.cfg scale.yml -b -v \
  --private-key=~/.ssh/private_key
```

可以通过运行升级群集剧本来升级您的群集。参考 [Upgrades](https://github.com/kubernetes-incubator/kubespray/blob/master/docs/upgrades.md).
```
git fetch origin
git checkout origin/master
ansible-playbook upgrade-cluster.yml -b -i inventory/inventory.cfg -e kube_version=v1.6.0
```

##### Cleanup (reset.yml)
运行 `reset.yml` 进行重置节点清除 Kubespray 安装的所有组件。

