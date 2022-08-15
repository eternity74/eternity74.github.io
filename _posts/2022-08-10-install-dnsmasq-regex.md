---
published: true
---
## asus 공유기에 regex 가능한 dnsmasq 설치하기

asus 공유기에 기본 설치되어 있는 dnsmasq 에는 config 에 regex를 사용할 수 없다.
특정 스트링이 들어가 있는 도메인에 접속을 제한 하고 싶을때 regex를 사용하면 편리하다.
regex 패치가 되어 있는 dnsmasq를 빌드하여 기본 dnsmasq를 대체해보자.

https://github.com/lixingcong/dnsmasq-regex 프로젝트를 사용하여 빌드한다.
해당 프로젝트의 설치 방법을 보면 libpcre3-dev 를 설치하라고 되어 있는데 entware에는 해당 패키지가 존재하지 않는다. pcre.h 파일을 https://sourceforge.net/projects/pcre/files/pcre/8.45/ 소스에서 추출하여 빌드에 사용하기로 한다.

git clone 및 pcre link에 필요한 패키지 설치
    # opkg install libpcre git-http
    
dnsmasq-regex 소스 받아 오기
    # cd /opt/root
    # git clone --depth=1 https://github.com/lixingcong/dnsmasq-regex
    # cd dnsmasq-regex
    # . update_submodule.sh
    
pcre.h 파일을 /opt/root/dnsmasq-regex/ 폴더에 복사한다. [첨부된 파일](https://github.com/eternity74/eternity74.github.io/files/9331265/pcre.h.txt)을 사용하거나, https://sourceforge.net/projects/pcre/files/pcre/8.45/ 에서 추출한다.
복사한 파일이 사용될 수 있도록 Makefile을 수정한다. (컴파일 옵션과 링크 옵션 수정)
    
    diff --git a/Makefile b/Makefile
    index 914c7f6..cef50de 100644
    --- a/Makefile
    +++ b/Makefile
    @@ -5,16 +5,16 @@ PATCHES    := $(sort $(wildcard $(PATCH_DIR)/*.patch))
    
     PATCHED    := $(sort $(patsubst $(PATCH_DIR)/%.patch, $(PATCH_DIR)/%.patched, $(PATCHES)))
     # turn on/off for regex or regex_ipset
    -DNSMASQ_COPTS="-DHAVE_REGEX -DHAVE_REGEX_IPSET"
    +DNSMASQ_COPTS="-DHAVE_REGEX -DHAVE_REGEX_IPSET -I/opt/root/dnsmasq-regex"

     all:$(BIN)

     .PHONY: submodule
     submodule:
    -       cd dnsmasq && $(MAKE) COPTS=$(DNSMASQ_COPTS)
    +       cd dnsmasq && $(MAKE) COPTS=$(DNSMASQ_COPTS) LIBS=/opt/lib/libpcre.so

     $(BIN):$(PATCHED)
    -       cd dnsmasq && $(MAKE) COPTS=$(DNSMASQ_COPTS)
    +       cd dnsmasq && $(MAKE) COPTS=$(DNSMASQ_COPTS) LIBS=/opt/lib/libpcre.so
            $(MAKE) remove_patched
            $(MAKE) reset_submodule

dnsmasq.conf 파일을 /opt/etc/dnsmasq.conf 가 디폴트로 사용되도록 수정한다,

    RT-AX86U-4488:/opt/root/dnsmasq-regex/patches# cat 500-opt.patch
    --- a/src/config.h
    +++ b/src/config.h
    @@ -212,7 +212,7 @@ RESOLVFILE
     #   if defined(__FreeBSD__)
     #      define CONFFILE "/usr/local/etc/dnsmasq.conf"
     #   else
    -#      define CONFFILE "/etc/dnsmasq.conf"
    +#      define CONFFILE "/opt/etc/dnsmasq.conf"
     #   endif
     #endif

이제 빌드를 시작한다.
    # make -j4
    
    cd dnsmasq && make COPTS="-DHAVE_REGEX -DHAVE_REGEX_IPSET -I/opt/root/dnsmasq-regex" LIBS=/opt/lib/libpcre.so
    make[1]: Entering directory '/opt/root/dnsmasq-regex/dnsmasq'
    Package libpcre was not found in the pkg-config search path.
    Perhaps you should add the directory containing `libpcre.pc'
    to the PKG_CONFIG_PATH environment variable
    No package 'libpcre' found
    Package libpcre was not found in the pkg-config search path.
    Perhaps you should add the directory containing `libpcre.pc'
    to the PKG_CONFIG_PATH environment variable
    No package 'libpcre' found
    make[2]: Entering directory '/opt/root/dnsmasq-regex/dnsmasq/src'
    ... <compiling> ...
    cc  -o dnsmasq cache.o rfc1035.o util.o option.o forward.o network.o dnsmasq.o dhcp.o lease.o rfc2131.o netlink.o dbus.o bpf.o helper.o tftp.o log.o conntrack.o dhcp6.o rfc3315.o dhcp-common.o outpacket.o radv.o slaac.o auth.o ipset.o pattern.o domain.o dnssec.o blockdata.o tables.o loop.o inotify.o poll.o rrfilter.o edns0.o arp.o crypto.o dump.o ubus.o metrics.o hash-questions.o domain-match.o nftset.o  /opt/lib/libpcre.so
    make[2]: Leaving directory '/opt/root/dnsmasq-regex/dnsmasq/src'
    make[1]: Leaving directory '/opt/root/dnsmasq-regex/dnsmasq'
    make remove_patched
    make[1]: Entering directory '/opt/root/dnsmasq-regex'
    find . \( -name \*.orig -o -name \*.rej \) -delete
    rm -rf patches/001-regex-server.patched patches/002-regex-ipset.patched
    make[1]: Leaving directory '/opt/root/dnsmasq-regex'
    make reset_submodule
    make[1]: Entering directory '/opt/root/dnsmasq-regex'
    git submodule foreach --recursive git reset --hard
    Entering 'dnsmasq'
    HEAD is now at 770bce9 Fix parsing of IPv6 addresses with peer from netlink.
    make[1]: Leaving directory '/opt/root/dnsmasq-regex'
    
빌드가 완료되면 dnsmasq 파일이/opt/root/dnsmasq-regex/dnsmasq/src/dnsmasq 에 생성된다.
    # /opt/root/dnsmasq-regex/dnsmasq/src/dnsmasq --version
    Dnsmasq version 2.87test8-16-g770bce9  Copyright (c) 2000-2022 Simon Kelley
    Compile time options: IPv6 GNU-getopt no-DBus no-UBus no-i18n regex(+ipset) no-IDN DHCP DHCPv6 no-Lua TFTP no-conntrack ipset no-nftset auth no-cryptohash no-DNSSEC loop-detect inotify dumpfile

    This software comes with ABSOLUTELY NO WARRANTY.
    Dnsmasq is free software, and you are welcome to redistribute it
    under the terms of the GNU General Public License, version 2 or 3.

regex(+ipset) 옵션이 추가된 것을 알 수 있다.

새로운 dnsmasq 가 asus router 에서 사용 될 수 있도록 설정해보자
우선 dnsmasq 를 /opt/bin 으로 복사해 준다.

    # RT-AX86U-4488:/opt/root/dnsmasq-regex# cp dnsmasq/src/dnsmasq /opt/bin/

/opt/etc/dnsmasq.conf 파일이 기본 conf-file 로 사용되기에 original 파일을 /opt/etc/dnsmasq.conf 파일에서 Include 해준다.

    # Include original conf first
    conf-file=/etc/dnsmasq.conf
    # Add custom configuration
    # regex address examples, match 'xxx.qq.com' and 'xxx.q.com'
	address=/:\.q{1,2}\.com$:/127.0.0.1
    
마지막으로 재부팅시 dnsmasq-regex가 동작되도록 post-mount 스크립트 수정

    mount --bind /opt/bin/dnsmasq /usr/sbin/dnsmask
    service restart_dnsmasq
