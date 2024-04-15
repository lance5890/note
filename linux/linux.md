
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
