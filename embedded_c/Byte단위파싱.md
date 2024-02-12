# typedef

기존 타입을 원하는 이름으로 정의(definition) 하는 방법
- `typedef` = 새로운 타입 이름 만들기

타입 명이 마음에 들지 않을 때 사용된다.

```c
#include <stdio.h>

int main()
{
    long long t = 19239239239123912392312;

    printf("%lld", t);

    return 0;
}
```

```c
#include <stdio.h>

typedef long long ll;

ll t = 19232912931239123912312391239;

printf("%lld", t);

return 0;
```

# 구조체

- 기본 타입들을 모아 새로운 타입을 만드는 문법
- 타입을 하나 생성하여, Trace로 살펴보자.

```c
#include <stdio.h>

struct ABC { // ABC : 구조체 이름
    int a; // a, b : 멤버 변수
    int b;
};

int main()
{   // t : 구조체변수
    struct ABC t; // C++에서는 상관 없지만 C에서는 struct라는 말을 반드시 넣어줘야 함. 이를 축약하기 위해 typedef를 사용.
    t.a = 10;
    t.b = 20;

    return 0;
}
```

## 구조체 초기화 방법
- ABC 구조체 변수 t를 생성할때는 초기화 가능
- 만들고 난 뒤, 한꺼번에 초기화는 불가능
    ex) `t = {4, 5};`
    - C++에서는 가능한 문법
    - ***C언어에서는 불가능한 문법***

**C++에서는 struct를 안 붙여도 되지만, C언어에서는 반드시 붙여줘야 한다.**
- 타입명 : struct ABC

> C에서 되는 것
> 1. 선택적 초기화 됨. 유용함 (C++ X)
>
> C++에서 되는 것
> 1. struct 키워드 생략 가능 (C X)
> 2. 선언한 이후에 초기화 가능 (C X)
> 3. 구조체 안에서 함수 만드는 것 (C X)
 
# 구조체 변수 만드는 방법
- 타입을 만들자마자 변수를 만들 수 있다.

```c
#include <stdio.h>

struct ABC {
    int a;
    int b;
} t1, t2;

int main()
{
    t1.a = 10;
    t1.b = 20;

    t2.a = 30;
    t2.b = 40;

    return 0;
}
```

## struct를 매번 붙이기 번거롭다면?
- typedef 를 통해 간략하게 표현 가능하다.

```c
#include <stdio.h>

struct ABC {
    int a;
    int b;
} t1, t2;

typedef struct ABC AB;

int main()
{
    // struct ABC a = {1, 2};
    // struct ABC b = {1, 3};
    // struct ABC c = {5, 4};
    AB a = {1, 2};
    AB b = {1, 3};
    AB c = {5, 4};

    return 0;
}
```

**자주 사용되는 더욱 축약된 방식**

```c
#include <stdio.h>
/*
struct _ABC_ {
    int a;
    int b;
};

typedef struct _ABC_ ABC;
*/

typedef struct _ABC_ {
    int a;
    int b;
} ABC;

int main()
{
    ABC a = {1, 2};
    ABC b = {1, 3};
    ABC c = {5, 4};

    return 0;
}
```

# Union

- struct와 문법이 매우 비슷하다.

**Union을 왜 쓸까?**
- 파싱을 편하게 할 수 있다.
- 유니온과 구조체를 묶어서 쓰면 효율이 좋다.

**union은 멤버끼리 값을 공유한다.**
멤버 변수끼리 같은 메모리를 쓴다.

```c
#include <stdio.h>

union ABC {
	int a;
	int b;
};

int main()
{
	union ABC x;

	x.a = 10;
	x.b = 20;

	return 0;
}

```

# 바이트 단위 파싱

**Union을 쓰는 이유**
- 바이트 단위 파싱을 편하게 할 수 있다.
- (유니온 하나만 갖고는 쓸만하지 않다.)
- Union과 Struct를 섞어 쓰면 바이트 단위로 자유자재로 파싱이 가능 (차후 학습 예정)
- 우선은 비트연산 / Union만 연습하고 Union + Struct 비트 파싱을 연습 할 예정

공용체 안쓰고 파싱해보기

나의풀이

```c
#include <stdio.h>
#include <stdint.h>

typedef struct _ABC_ {
	uint64_t g;
	uint8_t buf[8];
} ACON;

int main()
{
	ACON data;
	data.g = 0xABCD12345678CD01;
	// 64바이트를 8바이트로 쪼개야한다.
	// AB CD 12 34 56 78 CD 01
	// 16^7*AB
	for (int i = 0; i < 8; i++) {
		// 2진수로 하면 32비트씩
		data.buf[i] = 0xff * (data.g >> 32 * (8-i));
		printf("%02x\n", data.buf[i]);
	}

	return 0;
}

```

강사님 풀이

```c
#include <stdio.h>
#include <stdint.h>

int main()
{
	uint64_t g = 0xABCD12345678CD01;
	uint8_t buf[8];

	for (int i = 7; i >= 0; i--) {
		buf[i] = g & 0xFF;
		g >>= 8;
		printf("%02X ", buf[i]);
	}

	return 0;
}

```

**Union을 사용하여 64비트에 저장된 데이터를 8비트 단위로 파싱해보자.**
- 리틀 엔디안 형태로 출력한다.

나의 풀이
```c
#include <stdio.h>
#include <string.h>
#include <stdint.h>

int main(void)
{
    uint64_t g = 0xABCD12345678CD01;

    union UNI {
    	uint64_t data;
    	uint8_t buf[8];
    }a;

    a.data = g;
    printf("%llX\n", a.data);
    for (int i = 0; i < 8; i++) {
    	printf("%02X", a.buf[i]);
    }
}

```

강사님 풀이

```c
#include <stdio.h>
#include <stdint.h>

typedef union _ABC_ {
	uint64_t a;
	uint8_t b[8];
} ABC;

int main()
{
	ABC x;
	x.a = 0xABCD12345678CD01;

	for (int i = 0; i < 8; i++) { // 이 컴퓨터에서 활용하는 엔디안 방식으로 출력 (리틀)
		printf("%02X ", x.b[i]);
	}

	return 0;
}

```

> value를 출력하면 빅엔디안, buf[0]~[7] 출력하면 리틀엔디안
> - 내부적으로는 리틀엔디안으로 저장한다.

```c
uint64_t g = 0xABCD12345678CD01;

union ABC {
    uint64_t value;
    uint8_t buf[8];
};

union ABC t1 = { g };
```

# 중첩된 구조체

## 중첩된 구조체 선언

구조체 내부에 구조체를 만들 수 있다.
- 멤버 a, b, c

```c
#include <stdio.h>
#include <stdint.h>

struct Node {
	int a;

	struct {
		int b1;
		int b2;
	} b;

	int c;
};

int main()
{
	struct Node v = {0};

	v.a = 20;
	v.b.b1 = 50;
	v.b.b2 = 30;
	v.c = 10;
	return 0;
}

```

## 선택적 초기화

```c
int main()
{
    struct v = {
        .a = 10,
        .c = 20
    };

    return 0;
}
```

```c
int main()
{
    struct Node v = {
        .a = 10,
		.b = {
			.b1 = 150,
			.b2 = 250
		}, // 자동으로 들어가는 세미콜론 지워야함
        .c = 20
    };

    return 0;
}

```

**구조체 생성과 초기화 실습**

```c
#include <stdio.h>
#include <stdint.h>

typedef struct _Node_ {
	int da;

	struct {
		int ga_a;
		int ga_b;
	} ga;

	struct {
		int dc_1;
		int dc_2;
	} dc;

} Node;

int main()
{
    Node v = {
    		.da = 10,
			.ga = {
				.ga_a = 100,
				.ga_b = 200
			},
			.dc = {
					.dc_1 = 50,
					.dc_2 = 60
			}
    };

    return 0;
}

```

# 구조체와 union 활용

```c
#include <stdint.h>

int main()
{
	union UNI {
		uint16_t c; // 앞 8비트에 하나, 뒤 8비트에 하나
		
		struct {
			uint8_t a; // 8비트에 하나
			uint8_t b; // 8비트에 하나
		}d1;
	};

	union UNI t = {0xFACD};

	return 0;
}

```

```c
#include <stdint.h>

int main()
{
	union UNI {		
		struct {
			uint8_t a; // 8비트에 하나
			uint8_t b; // 8비트에 하나
		}d1;
		
		uint16_t c; // 앞 8비트에 하나, 뒤 8비트에 하나
	};

	union UNI t = {0xFA, 0xCD};

	return 0;
}

```

```c
#include <stdio.h>
#include <string.h>
#include <stdint.h>

int main(void)
{
    union UNI {
    	struct {
    		uint8_t a;
    		uint8_t b;
    	}d1;

    	struct {
    		uint8_t a;
			uint8_t b;
    	}d2;
    };

    union UNI t = {0xAB, 0xCD};
    // t.d1 = {0xAB, 0xCD};
    // t.d2 = {0xAB, 0xCD};
    return 0;
}

```

> cf. 빅 엔디안은 AMD 프로세서의 기본.

**배열 데이터 파싱**
```c
#include <stdio.h>
#include <stdlib.h>
#include <stdint.h>

int main()
{
	uint8_t target[7] = {0xAB, 0x12, 0x13, 0xFA, 0xAA, 0xFF, 0xA0};

	union _Data_ {
		uint8_t receiveData[7];

		struct {
			uint8_t head;
			uint8_t body[5];
			uint8_t tail;
		}msg;
	}dm;

	memcpy(&dm, target, 7); // 배열 그대로를 넣음

	printf("Head = %02X\n", dm.msg.head);
	printf("Body = ");
	for (int i = 0; i < 5; i++) {
		printf("%02X", dm.msg.body[i]);
	}
	printf("\n");
	printf("Tail = %02X\n", dm.msg.tail);

	return 0;
}

```

