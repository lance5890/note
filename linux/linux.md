
### Fio 
````
du -sh * (check the file size)
````

```azure
du -sh *
df -Th
du -d1 -h
```

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
// 运行iofsstat.py脚本
sudo nohup iostat -xz 2 -t > stat.log &
sudo nohup python3 ./iofsstat.py -d sdb -c 2 > iofsstat.log &


sudo iostat -xz 2 -t  |  查看系统负载
iostat -xm 2 /dev/sdc  | 查看特定磁盘


// 查询iostat 结果，过滤iowait大于100的情况
#!/bin/bash

sudo iostat -xz 2 -t   > stat.log 
awk '/09\/28\/2024/{print $0} /^sd/{ if($12 > 10) print $0;}' stat.log | grep -C1 -E "^sd" 

// 导处固件日志
/opt/MegaRAID/storcli/storcli64 /c0 show aliLog logfile=stro.log
/opt/MegaRAID/storcli/storcli64 /c0 show aliLog

pidstat -d 2 | 查看各个进程的io情况

sar -f sa05 |  sar -d -f sa05 | 查看CPU负载
sar -f  sa14 -q | 查看历史负载

iotop -b -d 1 -t | 查看环境io是否有异常
````


###

```azure
// 查看 /var/log/pods 日志目录
du -ah /var/log/pods | sort -rh

// 查看当前目录文件夹大小并排序
du -sh * | sort -h

// 查看磁盘占用情况
df -Th
```
### 查看CPU负载情况
````

ps -eLo pid,tid,psr,comm | grep -E "^[[:space:]]*[0-9]+[[:space:]]+[0-9]+[[:space:]]+0[[:space:]]+" | wc -l  | 查看核0上的进程数量

ps -eLo pid,tid,psr,comm | grep -E "^[[:space:]]*[0-9]+[[:space:]]+[0-9]+[[:space:]]+0[[:space:]]+" | awk -F":" '{for (i=1;i<=NF;i++) {a[$i]++}}END {for (i in a) print i,a[i]}'


ps -eLo pid,comm,%cpu,psr | 查看进程所在的CPU

// 根据关键词查询线程所在的pid
ps -eLo pid,tid,comm | grep fsync
// 根据pid查询进程运行情况
top -p xxxx
````

### 检查网络参数
```
# 检查连接数情况
ss -s 
```

### 查看D进程
```
ps -e -L  -o pid,psr,state,ucmd  | awk '{if($3=="D"){print $0}}'
ps -eL -o stat,pid,cmd,wchan | grep ^D
```

### 查看僵尸进程
```
ps -A -ostat,ppid,pid,cmd |grep -e '^[Zz]'
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


### 使用sysctl
```azure
sysctl -a | grep ip_local_port_range  ## 查看本地端口范围
```

### 时钟同步
```azure
# 查看时钟源情况
chronyc -n sources -v  
# 检查时钟差异
clockdiff -o
```


### 查询每个节点的时钟
```azure
for host in `kubectl get nodes -owide |sed '1d'|  awk '{print $6}'`; do (echo $host; ssh -i id_rsa -o StrictHostKeyChecking=no ccadmin@"$host" date) done
```

### iostat 查看io瓶颈
```azure
//r_await 平均读延迟，单位ms 
//w_await 平均写延迟，单位ms
iostat -xzm 2
```

### 检测磁盘信息
```azure
smartctl -g wcache /dev/sda
sudo dd if=/dev/sda of=/dev/null bs=1M(4k) count=1024 iflag=direct
```


### grep
```azure
// 搜索审计日志
sudo grep -rn "test" /var/log/kube-apiserver/
```

```

###
```azure
iptables -t nat -S -v
iptables -t nat -L -v
iptables -t nat -nL
ipset list KUBE-CLUSTER-IP
查看 hostport对应的iptables规则
iptables-save -t nat | grep KUBE-HOSTPORTS

// 查看所有iptables规则
iptables -L
```

### 日志输出到本地
```azure
/usr/bin/cluster-etcd-operator operator 2>&1 | tee -a /etc/kubernetes/cluster-backup/ceo.log  这样就可以了，ceo是打印到错误输出了，不是标准输出
```

### 检查存储多路径
```azure
// 检查失效的多路径
sudo multipath -ll | grep failed
```


### 用bpf统计进程占用sdb系统盘io情况
```azure
#!/home/ccadmin/os-debug/x86_64/bpftrace
/*
 * biolatency.bt        Block I/O latency as a histogram.
 *                      For Linux, uses bpftrace, eBPF.
 *
 * This is a bpftrace version of the bcc tool of the same name.
 *
 * Copyright 2018 Netflix, Inc.
 * Licensed under the Apache License, Version 2.0 (the "License")
 *
 * 13-Sep-2018  Brendan Gregg   Created this.
 */

BEGIN
{

        time("==== %H:%M:%S ");
        printf("Tracing block device I/O... Hit Ctrl-C to end.\n");
}

kprobe:blk_account_io_start,
kprobe:__blk_account_io_start
{
        $disk = ((struct request*)arg0)->rq_disk->disk_name;
        $len = ((struct request*)arg0)->__data_len;
        if (!strncmp($disk, "sd", 2)) {
                @queue[$disk]++;
        }
        if ($len > 60000) {
                @stacks[arg0] = comm;
                @start[arg0] = nsecs;
        }
}

kprobe:blk_account_io_done,
kprobe:__blk_account_io_done
{
        $disk = ((struct request*)arg0)->rq_disk->disk_name;
        if (!strncmp($disk, "sd", 2)) {
                @done[$disk]++;
        }
        if (@start[arg0]) {
                if (!strncmp($disk, "sdb", 3)) {
                        @from[@stacks[arg0]] = count();
                }
        }
        delete(@stacks[arg0]);
        delete(@start[arg0]);
}

interval:s:1
{
        time("\n==== %H:%M:%S ====\n");
        print(@done);
        print(@queue);
        print(@from);
        clear(@done);
        clear(@queue);
        clear(@from);
}

END
{
        clear(@queue);
        clear(@done);
        clear(@from);
}
```


```azure
// 查看默认网络网关的连接ip
ip r s default
ip r get x.x.x.1
```


###
```azure
# 检查配置生效，kubelet及线程运行在指定核上
systemctl show --property=CPUAffinity kubelet crio
ps -eLo pid,lwp,psr,comm | grep "kubelet\|crio"

# 查看所有容器的cpuset
printf "%-44s %-64s %-48s %s\n" NAMESPACE POD CONTAINER CPUSET
for cid in $(crictl ps -q); do
                            scope=$(find /sys/fs/cgroup/cpuset/ -name "crio-${cid}.scope")
if [ "${scope}" != "" ]; then
                         cpuset=$(cat ${scope}/cpuset.cpus)
    cinfo=$(crictl inspect $cid | jq -r '.status | .labels["io.kubernetes.pod.namespace"] + " " + .labels["io.kubernetes.pod.name"] + " " + .labels["io.kubernetes.container.name"]')
    printf "%-44s %-64s %-48s %s\n" $cinfo $cpuset
else
echo "Error: missing scope for $cid" >> /dev/stderr
    fi
done
```


```azure
# 查看掉盘与否
journalctl -u multipathd |grep 'active paths'
```