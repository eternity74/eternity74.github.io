---
published: true
categories: coding
title: 'windows 의 터미널에서 nvim 와 gcc 사용하기'
---
# windows 터미널에서 nvim 설치하기
윈도우에서 간단한 알고리즘 문제 풀이를 하기위해서 nvim 와 gcc 를 사용할 수 있는 환경셋업에 대한 기록이다.

nvim 을 사용하기위해서는 windows용(https://github.com/neovim/neovim/blob/master/INSTALL.md)를 설치하거나 msys64 환경에서 nvim을 설치하여 사용하는 방법이 있다. git, gcc 등의 다른 unix 계열 tool 을 사용하기 위해서 msys64 환경을 선택하였다.

주의할 점은 msys64 에서 nvim을 설치할때 MSVCRT 가 아닌 UCRT 환경의 빌드를 선택해야 한다는 것이다.
https://www.msys2.org/docs/environments/ 페이지를 읽어보면 UCRT가 MSVC의 호환성이 더 좋다고 한다.

pacman 으로 설치할 때에도 mingw-w64-x86_64-neovim 가 아닌 mingw-w64-ucrt-x86_64-neovim를 설치해야 한다.

ucrt 버전을 설치하지 않응면 nvim 에서 아래 명령어가 제대로 동작되지 않는다.
```
:lua print(os.date("%z"))
```

정상출력
```
+0900
```

또한 msys64에 설치된 git을 사용하면 git rev-parse --show-toplevel 명령어로 git repository의 directory를 가져올때 cygwin path가 얻어진다.
window의 cmd 에서 nvim 을 정상적으로 사용하기 위해서는 cygpath -w -f - 명령어로 처리를 해줘야 한다.
(ref. https://github.com/gitkraken/vscode-gitlens/issues/965)