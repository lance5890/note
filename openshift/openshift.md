
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