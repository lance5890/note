
### 去掉master节点角色
kubectl label nodes <your-node-name> node-role.kubernetes.io/master-

### 给节点打标签
kubectl label nodes <your-node-name> xxx=xxx



kubectl get --raw /api/v1/nodes/single1/proxy/stats/summary | grep containers -C 5


### 查看资源修改详情
kubectl get cm -n kube-system bootstrap -oyaml --show-managed-fields=true


###
```azure
// 查看用户的操作权限
kubectl auth can-i update clusterversions.config.ccos.io/finalizers --as=system:serviceaccount:kube-system:statefulset-controller
```

###
```
// 批量批复pending csr
kubectl get csr -A | awk '{print $1}' |  tail -n +2 | xargs -n1 kubectl certificate approve
```