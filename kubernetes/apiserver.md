```azure
// Apiserver审计日志过滤

sudo cat /var/log/kube-apiserver/audit.log | jq '.requestReceivedTimestamp+" "+.verb+" "+.user.username+" "+.requestURI' -r | awk '{print $2" "$3" "$4}' | sed 's/\?.*//g' | grep -v "^get" | sed 's/resourceVersion=[0-9]*//g' | sort | uniq -c | sort -rn | head -n 20
```

### grep
```azure
// 搜索审计日志
sudo grep -rn "test" /var/log/kube-apiserver/
```