---
published: true
categories: coding
title: '[nvim] diffview.nvim 사용시 gerrit 과 동일한 file sort 사용하기'
---
# diffview.nvim 사용시 gerrit 과 동일한 file sort 사용하기
간혹 gerrit 의 수정 사항과 내 local commit 의 내용을 비교할 때가 있다.
이런경우 neovim 의 diffview.nvim (https://github.com/sindrets/diffview.nvim) 플러그인을 사용하면 좌측에 commit의 파일 목록이 나타나고 오른쪽에 파일의 diff 가 표시된다.
그런데 gerrit 이 특이하게 file sort를 할때 cpp 파일 보다 header 파일을 위쪽에 보여준다.
따라서 diffview.nvim에서 보여주는 목록과 gerrit 상의 목록의 순서가 달라지게 된다.
이부분을 수정하기 위해서 file sort 를 약간 변경하여 사용하고 있다.
내가 요청한 pull request (https://github.com/sindrets/diffview.nvim/pull/497) 인데 아직 반영이 되진 않았다.