# k8s 网络原理

k8s 中，每一个 Pod 都有一个 IP 地址，这个模型被称作 `IP-per Pod`

## Linux 网络实践

关于 Linux 网络原理，《Kubernetes 权威指南（第四版）》第七章也有写，我把操作记录都记在下边这篇文章中了：

 [一次网络命令实践.md](../../Linux/Shell/一次网络命令实践.md) 

## K8s 网络实践

《Kubernetes 权威指南（第四版）》第七章，P440

首先，修改 docker 的网络，默认情况下，docker0 的地址段是 172.17.0.0/16，首先通过修改 `/etc/docker/daemon.json` 文件更改这一配置，加入以下字段：

```
 "bip": "10.1.10.1/24"
```

然后重启 docker ，运行	`ip a` 就可以看到 docker 的新 ip 地址了。

其他节点同理。地址分别是 10.1.20.1，10.1.30.1。。。。

然后添加路由规则：

```
$ sudo route add -net 10.1.20.0 netmask 255.255.255.0 gw 172.20.20.179
```

172.20.20.179 是 10.1.20.1 所在宿主机的IP地址。针对别的节点同理

然后布一个空的 k8s 集群，我这里使用 `rke` 部署，使用的是 Flannel 网络插件。

但是部署后，我发现我部署的 k8s 集群并不是使用的 docker0 网桥，而是使用的集群自己创建的网桥：cni0







