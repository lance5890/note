### 获取kubelet堆栈
```azure
kill -s SIGUSR2 `pidof  kubelet`
```

```azure
systemctl list-dependencies kubelet
```