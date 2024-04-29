
### Fio 
````
du -sh * (check the file size)
````

### Disk
````
lsblk -d -o name,rota
````


### 查询目录下文件数量，
````
find /var/log/ -type f | wc -l
````

### 查看系统负载
````
sudo iostat -xz 2  |  查看系统负载
sar -f sa05 |  sar -d -f sa05 | 查看CPU负载
sar -f  sa14 -q | 查看历史负载
````

### 查看CPU负载情况
````

ps -eLo pid,tid,psr,comm | grep -E "^[[:space:]]*[0-9]+[[:space:]]+[0-9]+[[:space:]]+0[[:space:]]+" | wc -l  | 查看核0上的进程数量

ps -eLo pid,tid,psr,comm | grep -E "^[[:space:]]*[0-9]+[[:space:]]+[0-9]+[[:space:]]+0[[:space:]]+" | wc -l
awk -F":" '{for (i=1;i<=NF;i++) {a[$i]++}}END {for (i in a) print i,a[i]}'


ps -eLo pid,comm,%cpu,psr | 查看进程所在的CPU
````

### 检查网络参数
```
# 检查连接数情况
ss -s 
```

### 查看D进程
```
ps -e -L  -o pid,psr,state,ucmd  | awk '{if($3=="D"){print $0}}'
```

### 查看系统启动错误
```
journalctl --list-boots  ### 查看历史重启
journalctl -p3 ### 看本次启动的0-3级错误
journalctl -b -1 -p3 ### 看上次启动的0-3级错误
journalctl -b -2 -p3 ### 看上上次启动的0-3级错误

ps aux | grep -E "\s+D" ### 查看D住进程
```

### 查看netns
```
ip netns exec 02a6da50-72d8-47c0-ba0c-0135fc34d008 ping 10.43.0.1 ### 从ns 中访问服务端口
ip netns      ### 查看当前netns列表
ls -l /var/run/netns/  ### 查看netns数据
ip netns exec fe5dc27e-42a5-4534-9e72-368e7f302819 tcpdump -nnvvv -i eth0 tcp and host 10.43.0.1 ### 抓包
ip netns exec fe5dc27e-42a5-4534-9e72-368e7f302819 iptables-save  ### 查看所有iptables规则
```

### 查看linux文件锁
```azure
sudo lsof /var/lib/containers/storage/{storage.lock,overlay-layers/layers.lock}
sudo lslocks | grep layers
```