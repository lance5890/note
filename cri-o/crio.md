```
crictl ps | sed 1d | grep " etcd \| kube-apiserver " -v | awk '{print $1}' | xargs crictl stop

# 如何快速找到大的容器目录
du -sh *只能统计当前目录，不能统计子目录。为了更快定位目录，使用du -h -d 10命令列出10层子目录内的信息，子目录层数可以根据情况调整。

# 完整命令   
du -h -d 10 /var/lib/containers/storage| sort -hr | head -50

# 列出容器名称和容器id列表
sudo crictl inspect `sudo crictl ps -a | awk '{print$1}' | grep -v CONTAINER` | jq -r '.| .status.labels."io.kubernetes.pod.name" + " " + .info.runtimeSpec.root.path'

# 看top 30的容器可读可写层用量：
sudo crictl stats -a -o json | jq '.stats[] | .writableLayer.usedBytes.value + " " + .attributes.labels["io.kubernetes.pod.namespace"] + " " + .attributes.labels["io.kubernetes.pod.name"]' -r | sort -rn | head -n 30
```

```
sudo podman images --digests | grep sha256xxx
```
