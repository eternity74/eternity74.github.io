---
published: false
---
## WSL2 를 외부에 접속하기 위한 방법 (bridged networking)
WSL2을 외부에서 접속하려고 하면 어려움이 많다. WSL2 는 기본적으로 internal 로 설정되기 때문에 외부에서 접속을 하려먼 port forwarding을 기본적으로 해줘야 한다. port forwarding 을 설정하려면 WSL2의 ip를 알아야 지정할 수 있는데, WSL2 는 고정 IP로 지정이 안되어 wsl2 의 ip를 얻어온 후 port forwarding을 설정해야 하는 불편함이 있다.

이 문제로 static ip로 설정 할 수 있도록 해달라는 많은 요청이 있는데, 아직까지 해결되지 않고 있는것 같다.

이 모든 문제를 한방에 해결 할 수 있는 방법을 찾았는데, 그 방법이 briged networking을 설하여 WSL2이 host와 별도로 ip를 할당 받도록 하는 것이다. 예를 들자면, host는 192.168.0.100 을 사용한다면 WSL2는 192.168.0.101 사용 하는 것이다.

이렇게 하면 host에서 WSL2로 port forwarding을 설정하지 않아도 외부에서 접근이 가능하게 된다.

Hyper-V의 WSL 가상 스위치를 External 로 network adapter를 저장하면 bridged networking이 되는데, host가 재 부팅 될 때마다 WSL 가상 스위치가 재 생성되기 때문에 부팅때마다 설정을 해줘야 한다.

아래 script 는 이것을 자동화 한 것이다.

# WSL 시작
wsl -- echo "$(date) Started"

# 외부로 연결한 network adapter 이름 가져오기
# Get-NetAdapter로 확인했을때 아래와 같이 출력되고 있어서 InterfaceDescription 가 AS로 시작하는 adapter를 선택하도록 하도록 한다.
# 이더넷 2  ASIX AX88179 USB 3.0 to Gigabit Et...#2  10 Up   00-0E-C6-8E-4B-6C  1 Gbps
$ifname = Get-NetAdapter -InterfaceDescription "AS*" | Select-Object -ExpandProperty "Name"

# 해당 adaptor 의 hyper-v 확장 가능 가상 스위치 체크 해제
Set-NetAdapterBinding -Name $ifname -ComponentID vms_pp -Enable $False

# Hyper-v WSL 가상 스위치를 External로 adapter 지정
# 같은 command를 두번 실행해야 제대로 설정이 된다. (첫번째는 실패, 두번째 성공 하는 듯)
Set-VMSwitch WSL -NetAdapterName $ifname
Set-VMSwitch WSL -NetAdapterName $ifname

# WSL 에 고정 IP 설정
# wsl 에서 



Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.
