# Addnode

因实验节点为物理机，无法通过虚拟机镜像进行交付，故开发此自动化加入集群脚本

## 场景

```shell
1、支持离线安装
执行./Addnode -P即可

2、支持x86、arm不同架构服务器同时安装（会自行判断服务器架构安装）

3、执行脚本服务也没网
wget https://jiaofu-tools.obs.cn-east-2.myhuaweicloud.com/nodefile.tar.gz -P /tmp
./Addnode -P

4、如在安装过程中遇到错误，之前的操作可重复执行，有相应判断
```

## 使用方式

### 下载

```shell
wget https://jiaofu-tools.obs.cn-east-2.myhuaweicloud.com/Addnode
chmod +x Addnode
```

### 查看使用

```shell
[root@ddavd ~]# ./Addnode 
Usage: ./Addnode [options]
  option: -{WP}
    -W         创建需要添加节点的 env.txt 模板，请修改为您的相应配置
    -P         读取默认配置文件 (env.txt) ，安装所有节点
```

### 生成配置文件

```shell
[root@ddavd ~]# ./Addnode -W
2022-11-15 14:01:18 INFO  Action begin: Environment
2022-11-15 14:01:18 INFO  请修改env.txt中需要添加的node信息
2022-11-15 14:01:19 INFO  Action successful: Environment
```

### 修改配置文件

```shell
[root@ddavd ~]# cat env.txt
# Master服务器密码
Master_Passwd=xxxxx

# 需要添加的node服务器密码
Node_Passwd=xxxxxx

# Master IP地址
MasterIp=192.168.88.204

# 需要添加进Kubernetes集群的Node节点IP,空格分隔
NodeIpArray=192.168.88.48 192.168.55.57
```

### 加入集群

```shell
[root@ddavd ~]# ./Addnode -P
2022-11-15 12:55:32 INFO  Action begin: Disposable_Install
2022-11-15 12:55:32 INFO  下载安装资源包,  nodefile.tar.gz
2022-11-15 12:55:32 INFO  curl -O --retry 3 https://jiaofu-tools.obs.cn-east-2.myhuaweicloud.com/nodefile.tar.gz -o /tmp/
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  266M  100  266M    0     0  12.0M      0  0:00:22  0:00:22 --:--:-- 12.2M
2022-11-15 12:55:33 WARN  sshpass已安装
2022-11-15 12:55:38 WARN  192.168.88.204 存在/root/.ssh/id_rsa.pub
2022-11-15 12:55:58 WARN  192.168.88.48 存在/root/.ssh/id_rsa.pub
2022-11-15 12:55:59 WARN  192.168.55.57 存在/root/.ssh/id_rsa.pub
2022-11-15 12:55:59 INFO  Install_requirement : 从Master 192.168.88.204 拷贝集群文件到本地文件夹  /tmp/nodefile/k8s ...
2022-11-15 12:55:59 DEBUG scp -r 192.168.88.204:/etc/kubernetes/{bootstrap.kubeconfig,kube-proxy.kubeconfig,kubernetesTLS,config} /tmp/nodefile/k8s
2022-11-15 12:56:30 DEBUG scp -r 192.168.88.204:/etc/etcd/etcdSSL/ /tmp/nodefile/k8s
2022-11-15 12:56:41 INFO  服务器 192.168.88.48 为 x86_64 架构,执行 x86_64 架构安装 
2022-11-15 12:56:41 INFO  Install_requirement : 向 192.168.88.48 拷贝安装文件...
2022-11-15 12:56:41 DEBUG Install_requirement : scp /tmp/nodefile/down/amd64/* 192.168.88.48:/tmp/nodefile
2022-11-15 12:57:02 DEBUG Install_requirement : scp /tmp/nodefile/base/k8s.conf 192.168.88.48:/tmp/nodefile
2022-11-15 12:57:03 DEBUG Install_requirement : scp /tmp/nodefile/base/ipvs.sh 192.168.88.48:/tmp/nodefile
2022-11-15 12:57:03 DEBUG Install_requirement : scp -r /tmp/nodefile/k8s/bootstrap.kubeconfig 192.168.88.48:/tmp/nodefile
2022-11-15 12:57:03 DEBUG Install_requirement : scp -r /tmp/nodefile/k8s/kube-proxy.kubeconfig 192.168.88.48:/tmp/nodefile
2022-11-15 12:57:03 DEBUG Install_requirement : scp -r /tmp/nodefile/k8s/kubernetesTLS 192.168.88.48:/tmp/nodefile
2022-11-15 12:57:04 DEBUG Install_requirement : scp -r /tmp/nodefile/k8s/config 192.168.88.48:/tmp/nodefile
2022-11-15 12:57:04 DEBUG Install_requirement : scp -r /tmp/nodefile/k8s/etcdSSL 192.168.88.48:/tmp/nodefile
2022-11-15 12:57:04 INFO  获取ETCD集群地址
2022-11-15 12:57:07 DEBUG https://192.168.88.204:2379,https://192.168.88.205:2379,https://192.168.88.206:2379
2022-11-15 12:59:20 INFO  Init_server : 关闭防火墙
2022-11-15 12:59:20 INFO  Init_server : 关闭Selinux
setenforce: SELinux is disabled
2022-11-15 12:59:20 INFO  Init_server : 关闭Swap
2022-11-15 12:59:20 INFO  Base_install : 基础软件安装...
2022-11-15 12:59:20 INFO  Base_install : 同步时间
15 Nov 12:57:14 ntpdate[10576]: step time server 202.118.1.81 offset -132.805059 sec
2022-11-15 12:57:15 INFO  Base_install : 服务器调优
2022-11-15 12:57:15 INFO  Base_install : 开启ipvs转发

Unit docker.service could not be found.
2022-11-15 12:57:15 DEBUG Install_docker : 解压 docker-19.03.8.tgz
2022-11-15 12:57:17 DEBUG Install_docker : 安装 docker 中...
2022-11-15 12:58:00 INFO  Install_docker : enable and start docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
2022-11-15 12:58:05 INFO  Install_docker : docker安装successful

2022-11-15 12:58:05 INFO  Docker_load : 导入基础镜像
open /tmp/pause.tar.gz: no such file or directory
2022-11-15 12:58:05 INFO  Install_kubelet : 拷贝所需配置文件
2022-11-15 12:58:05 DEBUG Install_kubelet : cp -r /tmp/nodefile/k8s/etcdSSL /etc/etcd/
2022-11-15 12:58:05 DEBUG Install_kubelet : cp -r /tmp/nodefile/{bootstrap.kubeconfig,kube-proxy.kubeconfig,kubernetesTLS,config} /etc/kubernetes
2022-11-15 12:58:05 DEBUG Install_kubelet : cp -r /tmp/nodefile/kubelet /usr/local/bin/
2022-11-15 12:58:05 INFO  Install_kubelet : 拷贝kubelet.service
2022-11-15 12:58:05 INFO  Install_kubelet : 生成kubelet配置文件
2022-11-15 12:58:05 INFO  Install_kubelet : enable and start kubelet
2022-11-15 12:58:09 INFO  Install_kubelet : kubelet安装successful

Unit kube-proxy.service could not be found.
2022-11-15 12:58:10 DEBUG Install_kube-proxy : cp -r /tmp/nodefile/kube-proxy /usr/local/bin/
2022-11-15 12:58:10 INFO  Install_kube_proxy : 拷贝kube-proxy.service
2022-11-15 12:58:10 INFO  Install_kube_proxy : 生成proxy配置文件
2022-11-15 12:58:10 INFO  Install_kube-proxy : enable and start kube-proxy
Created symlink from /etc/systemd/system/default.target.wants/kube-proxy.service to /usr/lib/systemd/system/kube-proxy.service.
2022-11-15 12:58:14 INFO  Install_kube-proxy : kube-proxy安装成功successful

Unit flanneld.service could not be found.
2022-11-15 12:58:14 DEBUG Install_flannel
2022-11-15 12:58:14 INFO  Install_flannel : 生成 flanneld.service
2022-11-15 12:58:14 INFO  Install_flannel : 生成 /etc/sysconfig/flanneld
2022-11-15 12:58:14 INFO  Install_flannel : 配置Docker
2022-11-15 12:58:14 INFO  Install_flannel : enable and start flannel
Created symlink from /etc/systemd/system/multi-user.target.wants/flanneld.service to /usr/lib/systemd/system/flanneld.service.
Created symlink from /etc/systemd/system/docker.service.wants/flanneld.service to /usr/lib/systemd/system/flanneld.service.
2022-11-15 12:58:15 INFO  Install_flannel : 使用Flannel网络，重启Docker
2022-11-15 12:59:46 INFO  Install_flannel : flannel安装successful
2022-11-15 12:59:47 INFO  删除 192.168.88.48 安装包目录 /tmp/nodefile 
2022-11-15 12:59:47 DEBUG Join_Cluser : 查看是否有节点需要添加 kubectl get csr | grep -v NAME | grep Pending
node-csr-AKtpQnURf7cnzMSmdyOvBPrHGBClJSyl3LYTpywQjco   102s   kubelet-bootstrap   Pending
2022-11-15 12:59:48 INFO  Join_Cluser : 192.168.88.48 加入集群
certificatesigningrequest.certificates.k8s.io/node-csr-AKtpQnURf7cnzMSmdyOvBPrHGBClJSyl3LYTpywQjco approved
2022-11-15 13:00:06 INFO  查看集群 : kubectl get node -o wide
NAME             STATUS   ROLES    AGE    VERSION    INTERNAL-IP      EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION          CONTAINER-RUNTIME
192.168.88.205   Ready    <none>   403d   v1.13.12   192.168.88.205   <none>        CentOS Linux 7 (Core)   3.10.0-862.el7.x86_64   docker://19.3.5
192.168.88.206   Ready    <none>   403d   v1.13.12   192.168.88.206   <none>        CentOS Linux 7 (Core)   3.10.0-862.el7.x86_64   docker://19.3.5
192.168.88.207   Ready    <none>   403d   v1.13.12   192.168.88.207   <none>        CentOS Linux 7 (Core)   3.10.0-862.el7.x86_64   docker://19.3.5
192.168.88.208   Ready    <none>   403d   v1.13.12   192.168.88.208   <none>        CentOS Linux 7 (Core)   3.10.0-862.el7.x86_64   docker://19.3.5
192.168.88.48    Ready    <none>   16s    v1.13.12   192.168.88.48    <none>        CentOS Linux 7 (Core)   3.10.0-862.el7.x86_64   docker://19.3.8
2022-11-15 13:00:14 INFO  服务器 192.168.55.57 为 aarch64 架构,执行 aarch64 架构安装 
2022-11-15 13:00:14 INFO  Install_requirement : 向 192.168.55.57 拷贝安装文件...
2022-11-15 13:00:14 DEBUG Install_requirement : scp /tmp/nodefile/down/arm64/* 192.168.55.57:/tmp/nodefile
2022-11-15 13:00:31 DEBUG Install_requirement : scp /tmp/nodefile/base/k8s.conf 192.168.55.57:/tmp/nodefile
2022-11-15 13:00:31 DEBUG Install_requirement : scp /tmp/nodefile/base/ipvs.sh 192.168.55.57:/tmp/nodefile
2022-11-15 13:00:32 DEBUG Install_requirement : scp -r /tmp/nodefile/k8s/bootstrap.kubeconfig 192.168.55.57:/tmp/nodefile
2022-11-15 13:00:32 DEBUG Install_requirement : scp -r /tmp/nodefile/k8s/kube-proxy.kubeconfig 192.168.55.57:/tmp/nodefile
2022-11-15 13:00:32 DEBUG Install_requirement : scp -r /tmp/nodefile/k8s/kubernetesTLS 192.168.55.57:/tmp/nodefile
2022-11-15 13:00:32 DEBUG Install_requirement : scp -r /tmp/nodefile/k8s/config 192.168.55.57:/tmp/nodefile
2022-11-15 13:00:32 DEBUG Install_requirement : scp -r /tmp/nodefile/k8s/etcdSSL 192.168.55.57:/tmp/nodefile
2022-11-15 13:00:33 INFO  获取ETCD集群地址
2022-11-15 13:00:35 DEBUG https://192.168.88.204:2379,https://192.168.88.205:2379,https://192.168.88.206:2379
2022-11-15 13:00:35 INFO  Init_server : 关闭防火墙
2022-11-15 13:00:35 INFO  Init_server : 关闭Selinux
setenforce: SELinux is disabled
2022-11-15 13:00:35 INFO  Init_server : 关闭Swap
2022-11-15 13:00:36 INFO  Base_install : 基础软件安装...
2022-11-15 13:01:47 INFO  Base_install : 同步时间
15 Nov 13:01:54 ntpdate[24357]: adjust time server 84.16.73.33 offset -0.010797 sec
2022-11-15 13:01:55 INFO  Base_install : 服务器调优
2022-11-15 13:01:55 INFO  Base_install : 开启ipvs转发

Unit docker.service could not be found.
2022-11-15 13:01:56 DEBUG Install_docker : 解压 docker-19.03.8.tgz
2022-11-15 13:01:56 DEBUG Install_docker : 安装 docker 中...
2022-11-15 13:02:46 INFO  Install_docker : enable and start docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
2022-11-15 13:02:51 INFO  Install_docker : docker安装successful

2022-11-15 13:02:51 INFO  Docker_load : 导入基础镜像
open /tmp/pause.tar.gz: no such file or directory
Unit kubelet.service could not be found.
2022-11-15 13:02:51 INFO  Install_kubelet : 拷贝所需配置文件
2022-11-15 13:02:51 DEBUG Install_kubelet : cp -r /tmp/nodefile/k8s/etcdSSL /etc/etcd/
2022-11-15 13:02:51 DEBUG Install_kubelet : cp -r /tmp/nodefile/{bootstrap.kubeconfig,kube-proxy.kubeconfig,kubernetesTLS,config} /etc/kubernetes
2022-11-15 13:02:51 DEBUG Install_kubelet : cp -r /tmp/nodefile/kubelet /usr/local/bin/
2022-11-15 13:02:51 INFO  Install_kubelet : 拷贝kubelet.service
2022-11-15 13:02:51 INFO  Install_kubelet : 生成kubelet配置文件
2022-11-15 13:02:51 INFO  Install_kubelet : enable and start kubelet
Created symlink from /etc/systemd/system/multi-user.target.wants/kubelet.service to /usr/lib/systemd/system/kubelet.service.
2022-11-15 13:02:55 INFO  Install_kubelet : kubelet安装successful

Unit kube-proxy.service could not be found.
2022-11-15 13:02:56 DEBUG Install_kube-proxy : cp -r /tmp/nodefile/kube-proxy /usr/local/bin/
2022-11-15 13:02:56 INFO  Install_kube_proxy : 拷贝kube-proxy.service
2022-11-15 13:02:56 INFO  Install_kube_proxy : 生成proxy配置文件
2022-11-15 13:02:56 INFO  Install_kube-proxy : enable and start kube-proxy
Created symlink from /etc/systemd/system/default.target.wants/kube-proxy.service to /usr/lib/systemd/system/kube-proxy.service.
2022-11-15 13:03:00 INFO  Install_kube-proxy : kube-proxy安装成功successful

Unit flanneld.service could not be found.
2022-11-15 13:03:00 DEBUG Install_flannel
2022-11-15 13:03:00 INFO  Install_flannel : 生成 flanneld.service
2022-11-15 13:03:00 INFO  Install_flannel : 生成 /etc/sysconfig/flanneld
2022-11-15 13:03:00 INFO  Install_flannel : 配置Docker
2022-11-15 13:03:00 INFO  Install_flannel : enable and start flannel
Created symlink from /etc/systemd/system/multi-user.target.wants/flanneld.service to /usr/lib/systemd/system/flanneld.service.
Created symlink from /etc/systemd/system/docker.service.wants/flanneld.service to /usr/lib/systemd/system/flanneld.service.
2022-11-15 13:03:01 INFO  Install_flannel : 使用Flannel网络，重启Docker
2022-11-15 13:04:32 INFO  Install_flannel : flannel安装successful
2022-11-15 13:04:33 INFO  删除 192.168.55.57 安装包目录 /tmp/nodefile 
2022-11-15 13:04:33 DEBUG Join_Cluser : 查看是否有节点需要添加 kubectl get csr | grep -v NAME | grep Pending
node-csr-CLeBykeWv94teKRIQLX5hXBETgLuu6U6QtY1ouZJrrA   101s    kubelet-bootstrap   Pending
2022-11-15 13:04:33 INFO  Join_Cluser : 192.168.55.57 加入集群
certificatesigningrequest.certificates.k8s.io/node-csr-CLeBykeWv94teKRIQLX5hXBETgLuu6U6QtY1ouZJrrA approved
2022-11-15 13:04:45 INFO  查看集群 : kubectl get node -o wide
NAME             STATUS   ROLES    AGE     VERSION    INTERNAL-IP      EXTERNAL-IP   OS-IMAGE                   KERNEL-VERSION                CONTAINER-RUNTIME
192.168.55.57    Ready    <none>   10s     v1.13.12   192.168.55.57    <none>        CentOS Linux 7 (AltArch)   4.14.0-115.el7a.0.1.aarch64   docker://19.3.8
192.168.88.205   Ready    <none>   403d    v1.13.12   192.168.88.205   <none>        CentOS Linux 7 (Core)      3.10.0-862.el7.x86_64         docker://19.3.5
192.168.88.206   Ready    <none>   403d    v1.13.12   192.168.88.206   <none>        CentOS Linux 7 (Core)      3.10.0-862.el7.x86_64         docker://19.3.5
192.168.88.207   Ready    <none>   403d    v1.13.12   192.168.88.207   <none>        CentOS Linux 7 (Core)      3.10.0-862.el7.x86_64         docker://19.3.5
192.168.88.208   Ready    <none>   403d    v1.13.12   192.168.88.208   <none>        CentOS Linux 7 (Core)      3.10.0-862.el7.x86_64         docker://19.3.5
192.168.88.48    Ready    <none>   4m51s   v1.13.12   192.168.88.48    <none>        CentOS Linux 7 (Core)      3.10.0-862.el7.x86_64         docker://19.3.8
2022-11-15 13:04:49 INFO  Action successful: Disposable_Install
```