
### fio 测试磁盘性能是否符合etcd指标
```
#!/bin/bash

set -e

# Create etcd write dir if it doesn't exist
mkdir -p /tmp/etcd

# Run fio
echo "---------------------------------------------------------------- Running fio ---------------------------------------------------------------------------"
fio --rw=write --ioengine=sync --fdatasync=1 --directory=/tmp/etcd --size=22m --bs=2300 --name=etcd_perf --output-format=json | tee /tmp/fio.out
echo "--------------------------------------------------------------------------------------------------------------------------------------------------------"

# Scrape the fio output for p99 of fsync in ns
fsync=$(cat /tmp/fio.out | jq '.jobs[0].sync.lat_ns.percentile["99.000000"]')
echo "99th percentile of fsync is $fsync ns"

# Compare against the recommended value
if [[ $fsync -ge 10000000 ]]; then
echo "99th percentile of the fsync is greater than the recommended value which is 10 ms, faster disks are recommended to host etcd for better performance"
else
echo "99th percentile of the fsync is within the recommended threshold - 10 ms, the disk can be used to host etcd"
fi
```

### dd命名测试磁盘性能
```azure
dd if=/dev/zero of=/tmp/test bs=4k count=1024 oflag=direct

dd if=/dev/zero of=/tmp/test bs=4k count=1024 oflag=dsync
```

```azure
sudo curl -s --cacert /etc/kubernetes/static-pod-resources/etcd-certs/configmaps/etcd-serving-ca/ca-bundle.crt --key /etc/kubernetes/static-pod-resources/etcd-certs/secrets/etcd-all-certs/etcd-serving-$(hostname).key --cert /etc/kubernetes/static-pod-resources/etcd-certs/secrets/etcd-all-certs/etcd-serving-$(hostname).crt https://127.0.0.1:2379/metrics > /tmp/etcd_metric.txt
```

### 查询前30位的etcd数据占用情况
```
// 查询 /kubernetes.io/ key 为前缀的k8s资源
etcdctl get --prefix /kubernetes.io/ --keys-only | grep -v "^$" | rev | cut -d/ -f2- | rev | sort | uniq -c | sort -rn | head -n 30

// 查看 / 所有资源的排序
etcdctl get / --prefix --keys-only | grep -v "^$" | cut -d/ -f3 | sort | uniq -c | sort -rn


```
### 删除指定前缀的key
```
etcdctl del --prev-kv --prefix /kubernetes.io/secrets/kruise-system

etcdctl get --prefix /kubernetes.io/secrets --keys-only | grep -v "^$" | rev | cut -d/ -f2- | rev | sort | uniq -c | sort -rn | head -n 30 | awk '{print $2}' | xargs -n1 | xargs -n1 etcdctl del --prev-kv --prefix
```

```azure

// 遍历lease查询
for l in $(etcdctl lease list | grep -v found);do if [ `etcdctl lease timetolive $l -w json | grep 997142320965431125` ]; then  echo -n "$l ";fi;done

```

```azure
//遍历key，查询key
for key in $(etcdctl get /kubernetes.io/ --prefix --keys-only );do value=$(etcdctl get "$key" --print-value-only);if [[ "$value" == *"ceaedge-node-1"* ]]; then echo "$key" >> node_name.txt;fi;done

```