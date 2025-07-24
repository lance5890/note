```azure
// Apiserver审计日志过滤

sudo cat /var/log/kube-apiserver/audit.log | jq '.requestReceivedTimestamp+" "+.verb+" "+.user.username+" "+.requestURI' -r | awk '{print $2" "$3" "$4}' | sed 's/\?.*//g' | grep -v "^get" | sed 's/resourceVersion=[0-9]*//g' | sort | uniq -c | sort -rn | head -n 20
```

### 查看apiserver各个资源分类
kubectl get --raw=/metrics | grep apiserver_storage_objects | sort -r -g -k 2 

### grep
```azure
// 搜索审计日志
sudo grep -rn "test" /var/log/kube-apiserver/
```


### 检查apiserver请求
time curl -k -H "Authorization: Bearer $(kubectl get secret -n ccos-calico $(kubectl get sa -n ccos-calico calico-node -o jsonpath='{.secrets[0].name}') -o jsonpath='{.data.token}' | base64 -d)" \
-H "User-Agent: calico-node/v0.0.0 (linux/amd64) kubernetes/$Format" \
https://<apiserver-ip>:6443/api/v1/pods?limit=500&resourceVersion=0&resourceVersionMatch=NotOlderThan > /tmp/1.txt


time kubectl get --raw '/api/v1/pods?limit=500&resourceVersion=0&resourceVersionMatch=NotOlderThan' --as=system:serviceaccount:ccos-calico:calico-node