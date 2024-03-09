#### wsl2部署k8s 

**参考：**

- [基于 Kind 的 Kubernetes 环境部署 - 单节点](https://xyzghio.xyz/K8sByKind/) 



##### 安装kind

```shell
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.11.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind

kind --version
```

##### 安装k8s

```shell
sudo apt-get install -y apt-transport-https

sudo su curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 

cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```



##### 使用kind启动k8s

**k8s配置文件**

```yaml
# ./local-k8s.yaml
# Kind 创建K8s集群 配置文件

kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4

# 该集群包含2个控制平面节点、3个工作节点
nodes:
- role: control-plane
  # 设置端口映射, 便于通过宿主机访问节点
  extraPortMappings:
  - containerPort: 80
    hostPort: 8880
    listenAddress: "127.0.0.1"
  - containerPort: 443
    hostPort: 8443
    listenAddress: "127.0.0.1"
- role: control-plane
  # 设置端口映射, 便于通过宿主机访问节点
  extraPortMappings:
  - containerPort: 80
    hostPort: 9980
    listenAddress: "127.0.0.1"
  - containerPort: 443
    hostPort: 9443
    listenAddress: "127.0.0.1"
- role: worker
- role: worker
- role: worker
```



```shell
# 使用my-k8s-cluster-3.yaml配置文件,创建一个名为 my-k8s-cluster-3 的K8s集群
kind create cluster --name local-k8s --config local-cluster.yaml
```



##### 查看数据

查看集群名称列表

```shell
kind get clusters
```

删除集群及其所有启动的容器

```shell
kind delete cluster --name <集群名称>
```



