#前期准备：

1、配置好各节点hosts文件

2、关闭系统防火墙

3、关闭SElinux

4、关闭swap

5、配置系统内核参数使流过网桥的流量也进入iptables/netfilter框架中，在/etc/sysctl.conf中添加以下配置：

```
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
sysctl -p
```

# 安装 kubeadm

1、首先配置阿里云源

```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
EOF
yum -y install epel-release
yum clean all
yum makecache
```

<https://opsx.alibaba.com/mirror>     其中可以看到k8s搭建的一些资源

2、安装kubeadm相关工具包

```
yum -y install docker kubelet kubeadm kubectl kubernetes-cni
```

3、启动docker和kubelet服务

```
systemctl enable docker && systemctl start docker
systemctl enable kubelet && systemctl start kubelet
```

提示：此时kubelet的服务运行状态是异常的，因为缺少主配置文件kubelet.conf。但可以暂不处理，因为在完成Master节点的初始化后才会生成这个配置文件。

4、配置阿里云加速器    <https://cr.console.aliyun.com/>

提示：在阿里云上使用 Docker 并配置阿里云镜像加速器，可能会遇到 daemon.json 导致 docker daemon 无法启动的问题，可以通过以下方法解决。

```
你需要的是编辑
vim /etc/sysconfig/docker
然后
OPTIONS='--selinux-enabled --log-driver=journald --registry-mirror=http://xxxx.mirror.aliyuncs.com'
registry-mirror 输入你的镜像地址
最后 service docker restart 重启 daemon
然后 ps aux | grep docker 然后你就会发现带有镜像的启动参数了。
```

# 安装k8s

1、下载k8s相关安装包

```
#!/bin/bash
images=(kube-proxy-amd64:v1.10.0 kube-scheduler-amd64:v1.10.0 kube-controller-manager-amd64:v1.10.0 kube-apiserver-amd64:v1.10.0
etcd-amd64:3.1.12 pause-amd64:3.1 kubernetes-dashboard-amd64:v1.8.3 k8s-dns-sidecar-amd64:1.14.8 k8s-dns-kube-dns-amd64:1.14.8
k8s-dns-dnsmasq-nanny-amd64:1.14.8)
for imageName in ${images[@]} ; do
  docker pull keveon/$imageName
  docker tag keveon/$imageName k8s.gcr.io/$imageName
  docker rmi keveon/$imageName
done
```

在虚拟机中执行上面的脚本。 上面的shell脚本主要做了3件事，下载各种需要用到的容器镜像、重新打标记为符合k8s命令规范的版本名称、清除旧的容器镜像。提示：镜像版本一定要和kubeadm安装的版本一致，否则会出现time out问题。

rpm -qa|grep kubeadm     查看kubeadm版本

第一次下载完成之后，可以 通过docker images在虚拟机中查看到所有的docker 镜像，通过dokcer save可以完成docker镜像的离线下载，下次要用的时候，直接将所有的离线安装包导入到虚拟机中，通过docker load命令完成镜像的加载

或者可以搭建一个自己的harbor私库，然后将所有的镜像都push到harbor私库中，下次需要的时候直接从docker 私库中拉取相应镜像即可

2、初始化

```
kubeadm init --kubernetes-version=v1.10.0 --pod-network-cidr=10.244.0.0/16
```

提示：选项–kubernetes-version=v1.10.0是必须的，否则会因为访问google网站被墙而无法执行命令。这里使用v1.10.0版本，刚才前面也说到了下载的容器镜像版本必须与K8S版本一致否则会出现time out。

3、 配置kubectl认证信息

```
# 对于非root用户
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
# 对于root用户
export KUBECONFIG=/etc/kubernetes/admin.conf
也可以直接放到~/.bash_profile
echo "export KUBECONFIG=/etc/kubernetes/admin.conf" >> ~/.bash_profile
```

4、 安装flannel网络

```
mkdir -p /etc/cni/net.d/
cat <<EOF> /etc/cni/net.d/10-flannel.conf
{
“name”: “cbr0”,
“type”: “flannel”,
“delegate”: {
“isDefaultGateway”: true
}
}
EOF
mkdir /usr/share/oci-umount/oci-umount.d -p
mkdir /run/flannel/
cat <<EOF> /run/flannel/subnet.env
FLANNEL_NETWORK=10.244.0.0/16
FLANNEL_SUBNET=10.244.1.0/24
FLANNEL_MTU=1450
FLANNEL_IPMASQ=true
EOF
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/v0.9.1/Documentation/kube-flannel.yml
```

5、让node节点加入集群

6、验证k8s是否安装成功

```
# 查看节点状态
kubectl get nodes
# 查看pods状态
kubectl get pods --all-namespaces
# 查看K8S集群状态
kubectl get cs
```

如果安装错误，怎么清理环境重新安装呢：

```
kubeadm reset
```

