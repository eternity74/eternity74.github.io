---
published: true
categories: etc
title: '[etc] Xpenology boot usb 백업하기 (windows11)'
---
# Xpenology boot usb 백업하기
수년전에 NAS 를 사용하기 위해서 Xpenology 를 구성해 두었다. 사진백업 및 필요한 서비스를 운영해보기 위해서 만들었는데 실제 활용도가 없어서 사진 백업할때만 가끔 켜서 사용하곤 하였다.
boot usb 를 만들어 본지도 오래되기도 하고 갑자기 usb 메모리가 사망하게 되면 어떻게 복구할지 난감할 수 있을 것 같아서 boot usb를 백업/복구하는 방법에 대해서 조사해 보았다.

https://svrforum.com/software/349269 페이지에서 가이드를 찾을 수 있었는데 win32diskimager의 Read Only Allocated Paritions 옵션을 사용하면 usb 용량 전체를 img 로 백업하는 것이 아니라 사용한 파티션에 대해서만 img를 만들게 되어 작은 용량으로 백업을 할 수 있다는것을 알았다.

그런데 무슨 일인지 windows 11 에서 win32diskimager 에서 boot usb가 인식되지 않았다. usb의 파티션이 windows 에서 인식이 되어야 win32diskimager 에서 device 에서 선택이 가능한 것으로 보였다.

현재 windows11 에서 내가 예전에 만들어 놓은 Xpenology usb boot 가 인식되지 않아 사용이 불가능하였다.
해당 파티션이 인식되면 win32diskimager 사용이 가능할 것으로 생각되어 https://sourceforge.net/projects/ext2fsd/ ext2fsd 를 설치하고 해당 앱에서 usb의 첫번째 파티션을 드라이브로 맵핑하였다.

맵핑된 드라이브가 win32diskimager에서 인식이 되었고, Read Only Allocated Partitions 옵션을 선택하여 작인 사이즈의 img를 백업하는데 성공하였다.
