---
published: false
---
## asus 공유기에 regex 가능한 dnsmasq 설치하기

asus 공유기에 기본 설치되어 있는 dnsmasq 에는 config 에 regex를 사용할 수 없다.
특정 스트링이 들어가 있는 도메인에 접속을 제한 하고 싶을때 regex를 사용하면 편리하다.
regex 패치가 되어 있는 dnsmasq를 빌드하여 기본 dnsmasq를 대체해보자.

https://github.com/lixingcong/dnsmasq-regex 프로젝트를 사용하여 빌드한다.
해당 프로젝트의 설치 방법을 보면 libpcre3-dev 를 설치하라고 되어 있는데 entware에는 해당 패키지가 존재하지 않는다. 대신 libpcre를 설치하고 빌드해보면 pcre.h 파일이 없다는 컴파일 에러가 해결되는 것을 알 수 있다.

    # opkg install libpcre git-http
    # git clone https://github.com/lixingcong/dnsmasq-regex
    # cd dnsmasq-regex
    # . update_submodule.sh
    # make -j 4
    
    
Cloning into 'dnsmasq-regex'...
git: 'remote-https' is not a git command. See 'git --help'.

처음부터 에러가 난다. git-http 를 설치하면 해결된다.
    # opkg install git-http