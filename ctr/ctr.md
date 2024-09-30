1.pull镜像
ctr -n k8s.io  i  pull -k --all-platforms harbor.ceclouddyn.com/ccos/general-op:CECStack3.0.1_rc1_1022
2.打成jar包
ctr -n k8s.io  i export --all-platforms  general_op_rc1.0.tar harbor.ceclouddyn.com/ccos/general-op:CECStack3.0.1_rc1_1022
3.转到目标服务器
scp general_op_rc1.0.tar root@10.255.71.70:/root/lubsh/general_op_rc1.0.tar
4.导入本地镜像

ctr -n k8s.io i import --all-platforms general_op_rc1.0.tar   //containerd
5.push 镜像仓库
ctr -n k8s.io i push -u admin:Harbor12345 --tlscacert /etc/containerd/certs.d/harbor.ceclouddyn.com/tls.crt harbor.ceclouddyn.com/ccos/general-op:CECStack3.0.1_rc1_1022