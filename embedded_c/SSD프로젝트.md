# 메인 함수 매개변수 (`Main()` Argument)

**C언어의 `main()` 함수는 실행 파일 옵션을 매개 변수로 받을 수 있다.**

- `argc` : main()에 전달되는 정보의 개수
- `argv[]` : main() 에 전달되는 정보. 첫 번째 문자열은 실행 경로로 고정

```c
#include <stdio.h>

int main(int argc, char* argv[]) {
    printf("argc = %d\n", argc);
    printf("argv = %s\n", argv[0]);
    return 0;
}

```

위 코드 심화

```c
#include <stdio.h>

int main(int argc, char* argv[]) {
	printf("argc = %d\n", argc);
	for (int i = 0; i < argc; i++) {
		printf("argv[%d] = %s\n", i, argv[i]);
	}
	return 0;
}


```

`git bash` 혹은 `cmd`로 실행한 결과

> SSAFY@DESKTOP-DOGVPUB MINGW64 ~/source/repos/ConsoleApplication1/Debug
> $ ./ConsoleApplication1.exe hi hello 1234
>
> argc = 4
> argv[0] = C:\Users\SSAFY\source\repos\ConsoleApplication1\Debug\ConsoleApplication1.exe
> argv[1] = hi
> argv[2] = hello
> argv[3] = 1234

# SSD Test Shell 제작 프로젝트

## 가상 SSD 제작

### SSD 테스트

**SSD 제품을 테스트 할 수 있는 Test Shell 을 제작한다.**
1. SSD를 가상으로 프로그래밍으로 구현한다.
2. Test Shell 프로그램을 제작하여 SSD 동작을 테스트 할 수 있다.
3. 다양한 Test Script 를 제작한다.

### 구현해야 할 프로그램

1. SSD
    - Hardware 를 Sofrware로 구현한다.
2. Test Shell Application
    - 테스트 프로그램
3. Test Script
    - 테스트 프로그램 내의 Test Code

### SSD 사전 지식

**저장할 수 있는 공간**
- 저장할 수 있는 최소 공간의 사이즈는 4KB
    ( 한 글자 = 약 1 Byte 으로 간주했을 때 4,096 글자 저장 가능 공간)
- 각 공간마다 LBA (Logical Block Address) 라는 주소를 가짐
- SSD 는 OSF로부터 Read / Write / Unmap 등 다양한 명령어를 전달받는다.

### 구현해야 할 가상 SSD

**최소화된 기능 수행**
- Read 명령어와 Write 명령어만 존재
- LBA 단위는 4Byte (실제는 4KB이지만, 우리가 만들 최소 저장공간 사이즈는 4Byte)

=> 총 400 Byte를 저장할 수 있는 가상 SSD 를 구현


## Test Shell Application

## Test Script 작성하기