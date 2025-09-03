---
published: true
categories: router
---
## Asus ax86u 공유기에 entware 설치하기

merlin firmware가 아닌 정펌에서 entware를 설치하고 재부팅시 entware가 동작되도록 설정하는 방법에 대한 기록이다.

usb 메모리를 공유기에 설치한다.

공유기에 접속하여 ssh 을 enable 하고 ssh 로 터미널접속한다.

usb 메모리를 ext3 으로 포맷한다.
    # blkid (포맷할 usb device 확인)
    # mke2fs -t ext3 /dev/sda1

최근 firmware에는 /opt 디렉토리내에 내용이 존재한다.. 따라서...
usb 메모리에 /opt/scripts 폴더를 복사한다. 
(usb 메모리를 /opt에 mount 할 것이라 원래 있었던것을 복사한다: 혹시 필요할지 몰라서...)

    # cp -R /opt/scripts /dev/sda1/

usb 메모리를 /opt 디렉토리에 mount 한다.

    # mount --bind /dev/sda1 /opt

entware 를 설치한다. (공유기별 설치 스크립트는 [link](https://github.com/Entware/Entware/wiki/Install-on-Asus-stock-firmware) 참조)

    # wget -O - http://bin.entware.net/aarch64-k3.10/installer/generic.sh | sh

usb 를 main으로 lable 수정

    # opkg install tune2fs
    # tune2fs -L main /dev/sda1
    
재부팅시 자동으로 entware가 mount 되도록 스크립트 설치
(이방법이 더이상 동작되지 않는다. 새로운 방법: https://github.com/jacklul/asuswrt-scripts/tree/master/asusware-usbmount)

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
