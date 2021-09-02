---
published: false
---
## memory를 hexdump해보자

golang에 익숙해지기 위해서 golang의 type 들에 대해서 좀더 깊이 이해하기 위해서 내부적으로 사용하는 data를 pointer를 이용해서 출력해서 확인해보고 싶었다. 물론 잘 만들어진 package가 있을 지도 모르지만 hexdump 함수를 만들어 보면서 golang을 학습하는 효과도 있으니까 만들어 보자.

# 우선 memory에 직접 접근해서 해당 메모리를 읽어야 하니 pointer를 사용한다.
가장 쉬운 예제로 byte array를 한번 찍어 보자.



Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.
