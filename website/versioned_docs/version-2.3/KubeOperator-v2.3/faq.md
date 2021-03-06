---
id: version-2.3-faq
title: 十、常见问题
original_id: faq
---

## 1 KubeOperator 的定位是什么？

云原生正在快速兴起，三个互相关联的领域在同步进化：

-  基础设施方面：从 物理资源 到 虚拟化资源 到 容器化（ Kubernetes ）资源 的演进；
-  开发模式方面：从 瀑布模型 到 敏捷开发 到 DevOps 的演进；
-  应用架构方面：从 单体架构 到 多层次架构 到 微服务 的演进。

KubeOperator 专注于解决基础设施层面的问题，为开发者交付生产级别可用的 K8s 集群，实现 Kubernetes as a Service。

## 2 KubeOperator 与 Kubespray 等部署工具的区别是什么？

KubeOperator 不仅提供 Day 1 部署功能，还提供 Day 2 的 Kubernetes 集群升级、扩容、监控检查、备份恢复等功能，如下图所示。

![overview](../../../img/overview.png)

KubeOperator 不仅支持安装程序本身，还提供了一组工具来监视 Kubernetes 集群的持续运行。KubeOperator 的优势包括：

-  提供可视化的 Web UI，大大降低部署和管理 K8s 的门槛；
-  提供离线的、经过全面验证和测试的安装包；
-  与 VMware 和 Openstack 等云平台紧密对接，能够实现一键虚机自动创建和部署（基于 Terraform 和 Ansible）；
-  KubeOperator 会提供经过充分验证的成熟企业级存储和网络方案。

## 3 KubeOperator 与 OpenShift 等 PaaS 平台有什么区别？

- 红帽 OpenShift 是一个非常全面的容器云平台，既包括面向运维人员的集群管理功能集，也包括面向开发人员的 CI/CD、DevOps、服务目录等功能集。 
- KubeOperator 只专注于解决运维人员侧的问题，即专注于帮助运维人员部署和管理安全、稳定、生产级别的 Kubernetes 集群。此外，KubeOperator 对外开放 REST API，可以实现 Kubernetes as a Service，开发人员可以自助申请 Kubernetes 集群并完成自动化交付。

## 4 K8s 集群应该部署在物理机上面，还是 IaaS 平台上面？

总的来说，企业有两种方案选择：

- 基于物理机部署大的 K8s 集群：通过 namespace 实现租户的隔离；
- 基于 IaaS 平台之上部署多个 K8s 集群：为每个租户分配独立的 K8s 集群；

这两种方案各有好处，在 K8s 采纳初期，使用第二种方案更为理性，因为：

 - 如果是单一大集群，升级会影响所有租户，风险比较大；
 - IaaS 平台上有成熟的、基于软件定义的存储和网络方案，落地更容易和灵活；
 - KubeOperator 与 VMware、Openstack 等 IaaS 方案紧密集成，可以实现全栈的自动化，集群交付快，伸缩快；

 ## 5 采用原生 Kubernetes 有什么好处？

 总的来说，相对于发行版，采纳原生 Kubernetes 有两个好处：

 - Kubernetes 迭代很快，且只维护最新的三个大版本。如果采纳其他发行版，可能很容易出现和原生版本脱节的情况。
 - 由于 Operator 和 Helm 等日趋成熟，很多发行版的功能，比如 CI/ CD, Istio 等都可以通过 addon 方式部署到 K8s 集群里面。Kubernetes 集群及其里面的应用应该是分离的，各自迭代升级。

 ## 6 KubeOperator 支持哪些持久化存储？

 KubeOperator 支持三类存储：

 - NFS 存储：手动模式和自动模式下的集群都支持 NFS 作为持久化存储。
 - 自动模式下，KubeOperator 通过 VMware 提供的 vSphere Cloud Provider 支持企业级存储，包括 具体请参考： https://vmware.github.io/vsphere-storage-for-kubernetes/documentation/overview.html#vsphere-storage-concepts
 - 自动模式下，KubeOperator 通过 Openstack 提供的 Openstack Cloud Provider 对接 Cinder。Cinder 支持的存储种类包括：https://wiki.openstack.org/wiki/CinderSupportMatrix

Static and Dynamic PVs 的支持情况取决于所选择的存储。以 vSphere 平台为例，各种存储选项可以参考这篇文章：

 https://docs.vmware.com/en/VMware-Enterprise-PKS/1.5/vmware-enterprise-pks-15/GUID-vsphere-persistent-storage.html

## 7 K8s 集群里的 master 、 worker 节点以及相关内置应用默认用户名/密码？

如果是自动模式创建的 K8s 集群，集群中 master 和 worker 节点的默认用户名和密码为：`root / KubeOperator@2019` </br>
访问 Grafana 、Registry 和 Weave Scope 应用的默认用户名和密码是相同的都是：`admin / admin123` 
访问 Ceph 控制台用户名为 admin ，密码需要执行命令获取，获取方法如下：

``` bash
#此命令可以在集群概览页下面的 WebKubectl 中或者在集群中任意节点中执行。
$ kubectl -n rook-ceph get secret rook-ceph-dashboard-password -o jsonpath="{['data']['password']}" | base64 --decode && echo

```

## 8 KubeOperator 自身重启、升级或者挂掉会影响其创建和管理的 K8s 集群吗？

不会有任何影响。KubeOperator 是一个 100% 旁路系统，其与被管 K8s 集群完全解耦。

## 9 重启 KubeOperator 部署的 K8s 集群的节点后，比如 Master 或者 Worker 节点，会自动恢复正常吗？

会自动恢复正常。

## 10 KubeOperator 支持的 vSphere 版本是什么？

 - 如果是手动部署模式 + NFS，支持 vSphere 5.5 及以上版本。
 - 如果是自动模式 + vSAN，支持 vSphere 6.5 及以上版本

## 11 KubeOperator 仅支持 CentOS 7.6 Minimal 及以上版本作为 K8s 节点的操作系统吗？

 是。KubeOperator 的管理范围包括操作系统，比如操作系统补丁升级，其提供的离线包包括操作系统（自动模式）及其 RPM 包，一个离线包版本代表一个终态，并被充分测试和验证。

 > 注：KubeOperator 不支持 CentOS 8，目前支持的版本是 CentOS 7.4 7.5 7.6 7.7。

## 12 K8s 集群的升级策略是什么？

 KubeOperator 支持小版本的升级，比如 1.15.2 升级到 1.15.*， 不能升级到 1.16.* 。

## 13 KubeOperator 是否已通过云原生基金会的 Kubernetes 软件一致性认证？

  是的。KubeOperator 已经通过认证，具体请参加：https://landscape.cncf.io
  
## 14 KubeOperator 和 Rancher 有什么区别？

Rancher 是完整的容器管理平台，KubeOperator 仅专注于帮助企业规划、部署和运营生产级别的 K8s 集群，和 KubeOperator 有可比性的是 Rancher RKE，而不是 Rancher 全部。

KubeOperator 推荐企业采纳解耦的方式来实现云原生之路，也就是说容器云平台与其之上的 DevOps 平台、微服务治理平台、AI 平台、应用商店等是解耦的。

## 15 KubeOperator 是否支持用户自主选择 K8s 离线包版本？

  支持。
  目前支持的 K8s 离线包下载地址：https://github.com/KubeOperator/k8s-package/releases  </br>
  选择 K8s 离线包方法举例：</br>
  下载 kubernetes v1.15.5 离线包，目前离线包通过百度网盘下载。</br>
  链接：https://pan.baidu.com/s/1oiDyVc9J10gUzg4vjo5oTw </br>
  提取码：18vw 
  
 ``` bash
# 将 K8s 离线包 copy 到 KubeOperator 部署机下面目录中
# cd /opt/kubeoperator/data/packages
# 解压 K8s 离线包
tar zxvf k8s-package-1.15.5.tar.gz
# 修改 K8s 离线包权限
chmod -R 777 v1-15-5
# 在 KubeOperator Web 控制台【离线包】页，刷新后可以看到新添加的离线包，新建集群即可使用该版本。 
 ```
## 16 KubeOperator 改了哪些 Linux 内核参数？

### 内核参数
    net.ipv4.ip_forward = 1
    net.bridge.bridge-nf-call-iptables = 1
    net.bridge.bridge-nf-call-ip6tables = 1
    net.bridge.bridge-nf-call-arptables = 1
    net.ipv4.tcp_tw_recycle = 0
    net.ipv4.tcp_tw_reuse = 0
    net.netfilter.nf_conntrack_max=1000000
    vm.swappiness = 0
    vm.max_map_count=655360
    fs.file-max=6553600
    <----'ipvs only start'----->
    net.ipv4.tcp_keepalive_time = 600
    net.ipv4.tcp_keepalive_intvl = 30
    net.ipv4.tcp_keepalive_probes = 10
    <----'ipvs only end'------->

### 系统日志参数

    Storage=persistent
    SystemMaxUse=2G
    SystemMaxFileSize=200M
    MaxRetentionSec=2week
    ForwardToSyslog=no
    ForwardToWall=no

### 系统文件数限制

    DefaultLimitCORE=infinity
    DefaultLimitNOFILE=100000
    DefaultLimitNPROC=100000
    
### 其他
禁用SELinux<br>
关闭 swap

  
## 17 已知问题

- HA 方案暂不支持外部 lb 。

- 手动模式使用 Ceph 的集群，卸载后请重启节点以确保清除系统残留的虚拟网卡、路由信息、iptables|ipvs 规则等，否则重复使用这些机器会安装失败。

- 手动模式部署集群，如果主机选择 OpenStack 虚机，Pod 网络不通，解决方案是安全组需要打开 UDP 策略。
