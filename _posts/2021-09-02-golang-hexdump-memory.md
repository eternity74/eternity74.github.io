---
published: false
---
## memory를 hexdump해보자

golang에 익숙해지기 위해서 golang의 type 들에 대해서 좀더 깊이 이해하기 위해서 내부적으로 사용하는 data를 pointer를 이용해서 출력해서 확인해보고 싶었다. 물론 잘 만들어진 package가 있을 지도 모르지만 hexdump 함수를 만들어 보면서 golang을 학습하는 효과도 있으니까 만들어 보자.

### 우선 memory에 직접 접근해서 해당 메모리를 읽어야 하니 pointer를 사용한다.
가장 쉬운 예제로 byte array를 한번 출력해보자.

```go
package main

import (
	"fmt"
)

func main() {
	var data [100]byte
	for i := 0; i < 100; i++ {
		data[i] = byte(i)
	}
	fmt.Printf("%p\n", &data)
	fmt.Println(data)
}
```
실행결과
```
0xc000052070
[0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 43 44 45 46 47 48 49 50 51 52 53 54 55 56 57 58 59 60 61 62 63 64 65 66 67 68 69 70 71 72 73 74 75 76 77 78 79 80 81 82 83 84 85 86 87 88 89 90 91 92 93 94 95 96 97 98 99]

Program exited.
```
[Playground 에서 결과 확인](https://play.golang.org/p/ihtSODDIExm)

### array type읜 단일 메모리로 되어 있는 sole direct value part 를 가진다.
(refer https://go101.org/article/value-part.html)

golang의 type의 값은 두 가지 형태가 있는데 array 처럼 메모리 블록으로 되어 있는 type들과 multipe memory block 으로 되어 있는 slice 같은 type들로 나뉘게 된다. 자세한건 나중에 좀더 공부해 보도록 하고 일단 array type에 집중해 보자.

단일 메모리로 되어 있다는 이야기는 data := [10]byte{0,1,2,3,4,5,6,7,8,9} 와 같이 선언을 한다면 &data 로 읽혀지는 주소에 array 의 data가 연속된 메모리로 할당되어 있다는 의미이다. 위의 경우 data의 주소가 0xc000052070 임으로 아래와 같이 메모리에 값이 쓰여 있다는 의미이다.
```
0xc000052070 : 00   <---- &data
0xc000052071 : 01
0xc000052072 : 02
0xc000052073 : 03
....
```

위의 내용을 memory를 직접 print 해서 알아보도록 눈으로 확인해보자.

Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.