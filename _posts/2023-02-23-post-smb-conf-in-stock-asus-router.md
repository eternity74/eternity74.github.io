---
published: true
categories: router
title: '[공유기] ax86u stock firmware 에서 smb.conf 수정하기'
---
## asus router stock firmware 에서 post-smb-conf 스크립트 사용하기

samba 를 enable 해서 네트웍 드라이브로 연결하여 사용할때 username/password 없이 사용하고 싶은데 설정방법을 찾지 못했다. /etc/samba.conf 파일을 미리 생성해 두어도 samba 서비스가 시작될때 설정파일을 지우고 새로 생성하여 사용하도록 구성되어 있다.

merlin firmware를 사용하면 쉽게 해결이 되겠지만, asus router stock firmware를 사용하며서 smb.conf를 수정할 수 있는 방법을 설명하고자 한다.

우선 asus router 에서 samba 서비스를 어떻게 시작하는지 알아보자.

ax86u 의 open source 를 다운 받아 src/router/rc/usb.c 내용을 보면 아래와 같은 부분이 있다.
```
void
start_samba(void)
{
...
	unlink("/etc/smb.conf");
	unlink("/etc/samba/smbpasswd");

	/* write samba configure file*/
	system("/sbin/write_smb_conf");
...
}
```
samba를 시작하기전에 /etc/smb.conf 파일을 지우고, /sbin/write_smb_conf 를 실행하여 configure file를 생성하는 것을 알 수 있다.
참고로 write_smb_conf.c 소스 파일 위치는 src/router/libdisk/write_smb.conf.c 이다.

이제 본격적으로 write_smb_conf 가 실행된 뒤 /jffs/scripts/post-smb-conf 스크립트가 실행되도록 하는 방법에 대해서 알아보자.

mount --bind 를 사용하여 write_smb_conf 에 우리가 작성한 스크립트가 실행되도록 설정하는 방법을 사용한다.
1. 우선 original write_smb_conf 를 /opt/sbin/write_smb_conf 로 mount 한다.
```
    # touch /opt/sbin/write_smb_conf
    # mount --bind /opt/sbin/write_smb_conf /sbin/write_smb_conf
```
2. write_smb_conf.sh 파일을 생성한다.
```
    # cat <<EOF > /opt/sbin/write_smb_conf.sh
    #!/bin/bash
    /opt/sbin/write_smb_conf
    [[ -f /jffs/scripts/post-smb-conf ]] && source /jffs/scripts/post-smb-conf
    EOF  
    # chmod +x /opt/sbin/write_smb_conf.sh
```
3. write_smb_conf.sh를 /sbin/write_smb_conf 에 mount 한다.
```
# mount --bind /opt/sbin/write_smb_conf.sh /sbin/write_smb_conf
```
4. post-smb-conf 작성
```
# cat <<EOF > /jffs/scripts/post-smb-conf
cp /opt/etc/smb.conf /etc/smb.conf
EOF
```
미리 작성된 /opt/etc/smb.conf 파일을 /etc/smb.conf 로 copy 하도록 작성되어 있다.

리부팅될때 위 작업이 실행 될 수 있도록 /jffs/script/post-mount 스크립트에 내용을 추가한다.
```
# reset previous mounts for smb conf releated
umount /sbin/write_smb_conf
umount /opt/sbin/write_smb_conf
[[ ! -f /opt/sbin/write_smb_conf ]] && touch /opt/sbin/write_smb_conf

# mount original write_smb_conf to /opt/sbin/write_smb_conf
mount --bind /sbin/write_smb_conf /opt/sbin/write_smb_conf

# mount script to /sbin/write_smb_conf
# the script will call /sbin/write_smb_conf then /jffs/scripts/post-smb-conf
mount --bind /opt/sbin/write_smb_conf.sh /sbin/write_smb_conf
```
