---
published: true
category: router
---
## Asus ax86u 공유기에 entware 설치하기

merlin firmware가 아닌 정펌에서 entware를 설치하고 재부팅시 entware가 동작되도록 설정하는 방법에 대한 기록이다.

usb 메모리를 공유기에 설치한다.

공유기에 접속하여 ssh 을 enable 하고 ssh 로 터미널접속한다.

usb 메모리를 ext3 으로 포맷한다.

    # mke2fs -t ext3 /dev/sda1

usb 메모리를 /opt 디렉토리에 mount 한다.

    # mount /dev/sda1 /opt

entware 를 설치한다. (공유기별 설치 스크립트는 [link](https://github.com/Entware/Entware/wiki/Install-on-Asus-stock-firmware) 참조)

    # wget -O - http://bin.entware.net/aarch64-k3.10/installer/generic.sh | sh

usb 를 main으로 lable 수정

    # opkg install tune2fs
    # tune2fs -L main /dev/sdb1
    
재부팅시 자동으로 entware가 mount 되도록 스크립트 설치

    # nvram set script_usbmount="/jffs/scripts/post-mount \$1"

스트립트 작성

    # mkdir -p /jffs/scripts/

/jffs/scripts/post-mount 파일에 아래 내용 추가

    #!/bin/sh
    if [ "$1" == "/tmp/mnt/main" ]
    then
    sleep 5
    logger "mounting $1 to /opt"
    mount --bind $1 /opt
    /opt/etc/init.d/rc.unslung start
    fi
