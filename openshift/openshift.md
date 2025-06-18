
###
```azure
// 停止cvo
oc scale --replicas=0 deploy/cluster-version-operator -n openshift-cluster-version

oc auth can-i --as=system:serviceaccount:image-registry:ccos-base-image-registry use scc/privileged

oc adm policy who-can use scc  privileged

oc adm policy add-scc-to-user privileged system:serviceaccount:myproject:mysvcacct
```

###
```azure
// 拉取ocp镜像
podman login --authfile ./auth
podman pull quay.io/openshift-release-dev/ocp-release:4.16.7-x86_64
```

### 导出ceo堆栈
oc exec -n openshift-etcd-operator "${CEO_POD}" -- curl -s http://127.0.0.1:6060/debug/pprof/goroutine?debug=2 > ./ceo_pprof_goroutine_dump.txt