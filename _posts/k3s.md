+++
title = 'K3s'
date = 2024-03-31T14:26:16+08:00
draft = true
+++

###### Introduction

关于K3s的工作原理，它遵循与Kubernetes相同的基本原则：

- **Pods**：Kubernetes中的基本部署单元，可以包含一个或多个容器。
- **Nodes**：集群中的服务器，可以是物理机或虚拟机，分为Master节点和Worker节点。
- **Master节点**：负责管理集群状态，调度Pods到Worker节点上。
- **Worker节点**：运行应用程序的Pods。
- **Service**：定义了Pods的网络接口，通过Service可以访问运行在Pods中的应用。
- **Ingress**：管理外部访问集群内服务的规则。
- **ConfigMap和Secrets**：存储配置信息和敏感数据。

###### Push images to Docker hub

All commands in sequence

```bash
# Check the images and tag
docker images
# 使用docker tag命令为你的镜像创建一个标签，这个标签应该包括你的Docker Hub用户名、仓库名和可选的标签（例如：版本号）
docker tag server:latest asakax/kroxy:server
# 在终端中使用docker login命令登录到你的Docker Hub账户
docker login -u asakax
# 使用docker push命令将镜像推送到你的Docker Hub仓库
docker push asakax/kroxy:server
# Another image, running the same commands
docker tag fun_image:latest asakax/kroxy:proxy
docker push asakax/kroxy:proxy
```

##### Service

在Kubernetes中，当你配置一个Service时，你实际上是在创建一个在集群内部可以访问的稳定的地址，用来访问运行在Pods中的一组特定的容器。即使Pods被重新部署到不同的节点，Service也会保持不变，确保内部通信的连贯性。

在你的Service定义中，你设置了端口 `4747` 转发到 `8084`。这里：

- `4747` 是Service的端口，也就是你的集群内部其他服务或者应用将会使用这个端口来访问与这个Service关联的Pods。
- `8084` 是Pod内部容器监听的端口。当集群内的其他组件通过Service的 `4747` 端口发起请求时，Service将这些请求路由到后端Pod的 `8084` 端口。

因此，`4747` 是Service的端口，它是在Kubernetes集群的内部虚拟网络中；而 `8084` 是Pod里面的 `proxy-sidecar` 容器的端口，它是容器在启动时倾听的端口。

Service的作用是为Pods提供一个抽象层，这样你就不需要知道每个Pod的具体IP地址和端口。Service通过标签选择器来确定要将流量路由到哪些Pods。只要Pods匹配Service的选择器，流量就可以通过Service的端口转发到Pods的对应端口。

总结来说：

- **Service端口（例如 4747）**：是集群内其他服务或应用用来与Service通信的端口。
- **Pod端口（例如 8084）**：是Pod内部一个或多个容器实际监听的端口，用来接收Service转发的流量。

这种机制允许在不知道Pods具体IP地址的情况下，通过Service的稳定端口来访问Pods。这对于动态环境（如Pods因扩展或更新而频繁更换）尤为重要。

当你仅仅使用 `pod.yaml` 部署了一个Pod时，你创建的Pod默认只能在Kubernetes集群内部被访问。Pod内的容器可能在监听它们各自的端口，但这些端口不会被自动暴露给集群外部。与外界的通信需要通过Service资源来配置，而不同类型的Service提供不同级别的访问能力：

1. **ClusterIP**：这是默认的Service类型，它提供一个集群内部的虚拟IP，供集群内部的其他Pod访问。这个IP和端口组合只在集群内部有效，外部无法直接访问。

2. **NodePort**：这种类型的Service会在所有节点上打开一个特定的端口（在30000-32767范围内），并将该端口上的流量路由到Service。这意味着你可以通过 `<任意节点的IP>:<NodePort>` 来从集群外部访问Service。

3. **LoadBalancer**：这种类型的Service适用于云提供商环境，它会在Service前面分配一个负载均衡器。负载均衡器会分配一个外部IP，可以被集群外部访问。

4. **ExternalName**：这种类型的Service允许通过Kubernetes服务来访问外部资源，通过返回一个已经存在的外部的DNS名字。

所以，如果你需要从集群外部访问Pod，你通常需要创建一个Service，并根据你的具体需求选择合适的Service类型。例如，如果你在本地或者在没有负载均衡器的环境中，你可能会选择NodePort类型的Service。如果你在云环境中，你可能会选择LoadBalancer类型的Service。

##### Node

查看node的外部ip地址：

```bash
kubectl get nodes -o wide
```

端口转发(本地到service)：

```
kubectl port-forward svc/web-server-service 4746:4746
```

运行容器中的shell

```bash
# kubectl exec -it pod_name -c container_name -- /bin/sh
kubectl exec -it fun  -c proxy-sidecar -- /bin/sh
```
