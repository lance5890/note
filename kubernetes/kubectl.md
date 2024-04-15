
### 去掉master节点角色
kubectl label nodes <your-node-name> node-role.kubernetes.io/master-

### 给节点打标签
kubectl label nodes <your-node-name> xxx=xxx