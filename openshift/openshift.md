
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


### 查看证书
oc -n openshift-etcd get cm etcd-ca-bundle -o jsonpath="{.data.ca-bundle\.crt}" | openssl crl2pkcs7 -nocrl -certfile /dev/stdin | openssl pkcs7 -print_certs -text -noout


###
## 根据sa生成kubeconfig
```bash
# your server name goes here
server=https://localhost:8443

# sa ns and name
sa_ns=kube-system
sa_name=admin
# the name of the secret containing the service account token goes here
secret_name=$(kubectl get sa -n $sa_ns $sa_name -o json | jq .secrets[] -r | grep -- "-token-" | awk '{print $2}' | tr -d '"')

ca=$(kubectl get -n $sa_ns secret/$secret_name -o jsonpath='{.data.ca\.crt}')
token=$(kubectl get -n $sa_ns secret/$secret_name -o jsonpath='{.data.token}' | base64 --decode)

echo "
apiVersion: v1
kind: Config
clusters:
  - name: default-cluster
    cluster:
      certificate-authority-data: ${ca}
      server: ${server}
contexts:
  - name: default-context
    context:
      cluster: default-cluster
      namespace: default
      user: default-user
current-context: default-context
users:
  - name: default-user
    user:
      token: ${token}" > sa.kubeconfig
```

【bootstrap节点上kubeconfig丢失后环境拯救】
ccos login https://oauth-ccos.apps.cluster718.region718.cicd.cn:6443 -u admin -p 'Adm1n@123!^&' --insecure-skip-tls-verify