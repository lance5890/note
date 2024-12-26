### 获取kubelet堆栈
```azure
kill -s SIGUSR2 `pidof  kubelet`
```

```azure
systemctl list-dependencies kubelet
```

计算 /var/lib/kubelet的方式应该是  du -sh --exclude={*kubernetes.io~local-volume*,*kubernetes.io~csi*} /var/lib/kubelet