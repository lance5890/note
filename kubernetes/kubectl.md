
### 去掉master节点角色
kubectl label nodes <your-node-name> node-role.kubernetes.io/master-

### 增加worker节点角色
kubectl label nodes node-1 node-role.kubernetes.io/worker=

### 给节点打标签
kubectl label nodes <your-node-name> xxx=xxx

###
kubectl get pods -A --field-selector spec.nodeName=ipi-dev6911-master0 -owide

###
kubectl get --raw /api/v1/nodes/single1/proxy/stats/summary | grep containers -C 5

### patch 修改资源status
kubectl patch bmh -n machine-api worker1 --subresource status --type='json' -p='[{"op": "replace", "path": "/status/hardware/hostname", "value": "hehe"}]'

### 查看资源修改详情
kubectl get cm -n kube-system bootstrap -oyaml --show-managed-fields=true

###
```azure
// 查看用户的操作权限
kubectl auth can-i update clusterversions.config.ccos.io/finalizers --as=system:serviceaccount:kube-system:statefulset-controller
```

###
// 批量批复pending csr
kubectl get csr -A | awk '{print $1}' |  tail -n +2 | xargs -n1 kubectl certificate approve


### 
// 删除不健康的pod
kubectl get po -A | grep -v "Run\|Comp" |sed '1d'| awk '{print $1, $2}' | xargs -n2 kubectl delete pod  -n

kubectl delete pod --field-selector=status.phase==Failed --all-namespaces

###
// 查看sa对应的权限，但是注意区分ns
kubectl get rolebinding,clusterrolebinding --all-namespaces -o jsonpath='{range .items[?(@.subjects[0].name=="csi-nvmf-sa")]}{.roleRef.kind},{.roleRef.name}{"\n"}{end}'


### 从主机上拷贝二进制到pod中
kubectl cp cluster-version-util ccos-cluster-version/cluster-version-operator-86475bc788-bbp49:/tmp/cluster-version-util

kubectl cp ccos-etcd/etcd-ceaedge-node-1:/usr/bin/etcdctl /home/etcdctl



### 
kubectl cordon xxx

kubectl drain  xxx --ignore-daemonsets --force --delete-emptydir-data --timeout=30s