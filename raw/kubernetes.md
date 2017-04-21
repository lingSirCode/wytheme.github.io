# Kubernetes

[Documents](https://kubernetes.io/docs/)

## 基础

### 1. 用Minikube创建一个集群

Master： 管理集群状态

Nodes:

### Minikube
> Minikube is a tool that makes it easy to run Kubernetes locally. Minikube runs a single-node Kubernetes cluster inside a VM on your laptop for users looking to try out Kubernetes or develop with it day-to-day.

```bash
# 启动集群
minikube start --vm-driver=xhyve
# 配置
eval $(minikube docker-env)
# 关闭集群
minikube stop
```

## kubectl
```bash
# 查看 kube 集群状态
kubectl cluster-info
# kubedebug
kubectl cluster-info dump
# 查看相关组件状态
kubectl get pod,service,ReplicationController -l app=eva
# 查看pod状态
kubectl describe <podname>
```

## docker

[自制image](https://hub.docker.com/r/leno730/kube-addon-manager/)
[使用Squid3搭建Docker镜像下载代理](http://dockone.io/article/1380)
[docker依赖镜像汇总](https://hub.docker.com/r/ist0ne/kubedns-amd64/)

```bash
docker version
# docker 状态查看
docker info
# 查看有哪些镜像，核实镜像是否都正常安装
docker ps
# 登陆
docker login
# 翻墙方式 ？
proxychains4 docker pull ist0ne/pause-amd64
all_proxy=socks5://127.0.0.1:7070 docker pull ist0ne/pause-amd64
# kube 启动依赖image
docker pull ist0ne/kubernetes-dashboard-amd64
docker pull ist0ne/kubedns-amd64
docker pull ist0ne/pause-amd64
docker pull vicefree/kube-addon-manager:v6.1
```

## debug


### pod status stuck on 'container creating'

1. 找到对应pod名称

`$ kubectl get pod,service,ReplicationController -l app=eva`

2. 查看pod状态

`$ kubectl describe <podname>`

`Warning	FailedSyncError syncing pod, skipping: failed to "StartContainer" for "POD" with ImagePullBackOff: "Back-off pulling image \"gcr.io/google_containers/pause-amd64:3.0\""`

3. fix 通过docker hub拉取镜像

https://github.com/kubernetes/kubernetes/issues/6888
`docker pull xxx`

