---
published: true
categories: router
title: '[router] custom dhcpc_lease 스크립트 사용하기'
---

dhcpc_lease는 라우터에 dhcp로 ip를 할당 했을때 실행되는 스크립트이다.

asus router 에는 /sbin/dhcpc_lease 가 /sbin/rc 로 symlink 되어있다.
```
lrwxrwxrwx    1 root root             2 Apr 17 05:15 /sbin/dhcpc_lease -> rc
```

이전  custom dnsmasq 를 사용하도록 작업 해 두었다면 아래와 같이 설정한다.
[dnsmasq에서 regex사용하기 참조](router/post-smb-conf-in-stock-asus-router) 
 - /opt/etc/dnsmasq.conf 에 /etc/dnsmasq.conf 내용을 
 - dhcp-script=/sbin/dhcpc_lease 부분을 찾아서 comment 처리
```
#dhcp-script=/sbin/dhcpc_lease   # removed
```
 - 뒷 부분에 아래와 같이 추가
```
# Add custom configuration
dhcp-script=/opt/sbin/dhcpc_lease
```
 - /opt/sbin/dhcpc_lease 스크립트 생성
```
#!/bin/sh
# /sbin/dhcpc_lease

# Example:
# add f4:be:ec:ef:0b:3a 192.168.50.214 iPhone-13-Pro
event=$1
mac=$2
ip=$3
hostname=$4
logger "DHCP Lease Event: $event"
case "$event" in
    "add")
      logger "DHCP Lease Event: ${event} ${mac} ${ip} ${hostname}"
    ;;
esac

exec /sbin/dhcpc_lease "$@"
``` 
 - dnsmasq 재시작
```
service restart_dnsmasq
```

이제 /opt/sbin/dhcpc_lease 스크립트에서 ip가 새로 할당 되었을때 해당 device 에 대한 설정을 해줄 수 있다.
