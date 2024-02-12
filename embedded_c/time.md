> 시간
>
> - 시스템 시간
> - 시스템 클럭 (time)
>
> OS
> firmware
> cpu가 동작하기 위해서
> 클럭을 갖고 맞춰서 동작하고 있다.
>
> 시스템 시간
>
> - 실시간 성
> - 로그찍기 위해

# 시스템 시간

시스템과 시간은 매우 밀접한 연관이 있다.

## 컴퓨터 시스템의 현재 시간

- 1970년 1월 1일 0시 0분 0초부터 경과한 시간을 초 단위로 표현
- 시스템 클럭으로 유지됨
- `$date`

> 컴퓨터가 켜지고 CPU가 일을 하면 발열이 발생한다.
> 발열이 발생하면 cpu가 느리게 일하고, 클럭 도 느려진다.
> CPU 내부에는 자체적인 클럭 이 있지만 외부 클럭도 쓴다.
>
> - 지속적인 발열 문제로 외부 클럭을 사용하여
> - 지속적으로 실시간성 동기화 작업이 이뤄진다.

## UTC

Coordinated Universal Time

- 국제 표준 시간
- 1972년 부터 시행
- UTC + offset 으로 시간 표시
- 한국 = UTC + 9 = KST

## RTC

Real Time Clock = RTC

- 전원이 없어도 시간을 계산 함
- 수은 건전지 사용

> 소프트웨어 RTC와 하드웨어 RTC 두 종류가 있다.

## Linux에서 관리하는 시간

`$date` 명령어

- 부팅시 RTC 정보를 받아 리눅스에서 시간 정보를 관리
- 네트워크에 연결될 때, Time 서버에서 시간을 자동 update 한다.

`$hwclock` 명령어

- `$sudo hwclock`
- RTC HW 장치가 갖고 있는 시간 정보 값
- 리눅스에서는 date와 RTC가 맞지 않으면 한쪽으로 Sync를 맞춰주는 명령어를 써야함

인터넷 타임서버에서 시간 값 가져와 맞추기

- `$sudo apt install rdate`
- `$sudo rdate time.bora.net` (보라넷 타임서버)
- `$date` 로 현재시간 확인 (정확하다.)

`$hwclock` 명령어

- `$sudo hwclock -s`

  - 시스템 시간을 기준으로 RTC 시간을 변경
- 반대 명령어

  - `$hwclock -w` : RTC 시간을 기준으로 시스템 시간을 변경함

## 시스템 시간 vs 시스템 클럭

- 시스템 시간 : 컴퓨터 시스템의 현재 시간

  - 로그 파일 기록, 예약된 작업 수행, 파일 수정 시간 등
- 시스템 클럭 : H/W 장치

  - 운영체제에서 관리, CPU가 실행하는 명령어의 실행 주기를 결정
  - 타이머 구현, 정해진 간격으로 작업 수행, 실시간 처리, cpu 성능 향상 등

시스템 시간과 시스템 클럭은 동기화 되어 있다.

# RTOS (Reao Time OS)

실시간 시스템에 적합한 운영체제

- 임베디드 시스템에서 자주 사용한다.

요즘은 리눅스 기반의 RTOS가 사용된다.

- IOT 기술의 발달로 장치들이 정교화 되고 스마트 해짐
- 많은 레퍼런스로 개발이 용이하고, 유지 보수가 용이하다.

## RTOS vs LINUX

애초에 설계부터 다르다

- RTOS는 real time 을 위한 OS로 정해진 타임 라인을 정확히 지키기 위해 설계됨

하지만, 크게 보면 큰 차이는 없다

- 모든 OS는 preemptive (선제적인) 한 작업 수행을 보장

차이점 : RTOS는 `시스템 콜`에 대한 Preemptive 가 있다

> `shell` 이 `kernel` 에게 `syscall` 을 보내는데, `linux` 는 `kernel` 에만 preemptive가 있는 반면 `RTOS` 는 `syscall` 에도 preemptive 가 있다.

## RTOS 업계 순위

- VxWorks - Wind River
- Window Embedded Compact (구 Window CE) - Microsoft
- QNX - 블랙베리 (기사 : 전기차의 61%, 블랙베리 QNX로 달려...)
- 국산 NEOS - 한컴 MDS (국방, 항공용)

# time_t 와 tm

## time() 시스템콜

- `time_t time(time_t *tloc);`

  - 1970년 1월 1일 0시 0초부터 현재까지의 시간을 초 단위로 반환
- time_t type

  - 64비트 unsigned 정수형 숫자
  - 시간 표현 data type

```c
#include <stdio.h>
#include <time.h>

int main()
{
    time_t current_time = time(NULL);
    printf("%ld\n", current_time);
    return 0;
}
```

## 구조체 tm

C 표준 라이브러리에서 제공하는 시간과 날짜를 나타내기 위한 구조체

- localtime() 을 이용해서 구조체에 시간을 정보를 담을 수 있다. (int 형 멤버)

| 멤버    | 범위      |
| ------- | --------- |
| tm_sec  | 0-61      |
| tm_min  | 0-59      |
| tm_hour | 0-23      |
| tm_mday | 1-31      |
| tm_mon  | 0-11      |
| tm_year | 1900 이후 |
| tm_wday | 0-6       |

```c
#include <stdio.h>
#include <time.h>

int main()
{
    time_t current_time = time(NULL);
    struct tm *tmm = localtime(&current_time);
    printf("min : %d\n", tmm->tm_min);
    return 0;
}

```

## 실습 : 현재 시간 출력하기

출력 예시

```bash
2023/4/5 WED
22 : 33 : 48
```

1초에 한번 출력한다.

# clock_t

## clock()

`clock_t clock(void);`
- 현재 프로세스가 시작되고, 얼마나 시간이 흘렀는지 CPU 클럭 수치 값
- clock_t type

> 숫자가 클 수록 컴퓨터가 안 좋은것

```c
#include <stdio.h>
#include <time.h>

int main()
{
    clock_t a = clock();
    printf("%ld\n", a);
    return 0;
}

```

## clock 함수

CLOCKS_PER_SEC 매크로
- 1초당 시스템 clock이 올라가는 정도를 나타낸다.
- 어떤 수치가 나오는지 각자 확인해보자

> 출력값 : 1000000 (백만)

```c
#include <stdio.h>
#include <time.h>

int main()
{
    printf("%ld\n", CLOCKS_PER_SEC);

    return 0;
}

```

### 출력 결과 분석

- 469 clock == 469 us
- 515 clock == 515 us

## 실습 : 연산속도 측정하기

1억번 Loop 를 돌리는데 걸리는 시간 측정하기
- clock 을 시작 전 / 후 로 측정하면 us 단위가 된다.

- 3개 연산 속도를 비교해보자
덧셈 +, 나눗셈 /, 비트연산 <<

> `$gcc hi.c && ./a.out`

나의 코드

```c
#include <stdio.h>
#include <time.h>

int main()
{
    int n1 = 10;
    int n2 = 5;

    clock_t a = clock();
    int pp = 0;
    for (int i = 0; i < 1e9; i++)
    {
        pp = n1 + n2;
    }
    printf("%ld\n", a);

    clock_t b = clock();
    int dd = 0;
    for (int i = 0; i < 1e9; i++)
    {
        dd = n1 / n2;
    }
    printf("%ld\n", b);

    clock_t c = clock();
    int bb = 0;
    for (int i = 0; i < 1e9; i++)
    {
        bb = n1 << n2;
    }
    printf("%ld\n", c);

    return 0;
}

```

# gettimeofday

> 요즘에는 `time` 보다 `gettimeofday` syscall 을 더 많이 쓴다. 훨씬 더 정밀하기 때문

## `int gettimeofday(struct timeval *tv, struct timezone *tz);`
- `<sys/time.h>` 필요

```c
#include <stdio.h>
#include <sys/time.h>

int main()
{
    struct timeval time;

    gettimeofday(&time, NULL);

    printf("%ld\n", time.tv_sec);
    printf("%ld\n", time.tv_usec);
    return 0;
}
```

결과값

```bash
gihong@gihong-ssafy:~/workspace/time$ gcc gettimeofday.c && ./a.out
1680745500
983335
```

## timeval / timezone 구조체

- struct timeval
  - `time_t tv_sec` : 초
  - `suseconds_t tv_usec` : 마이크로 초

```c
struct timeval {
    time_t tv_sec;     /* seconds */
    suseconds_t tv_usec;    /* microsecnds */
}

```

- struct timezone (사용 안함)

```c
struct timezone {
    int tz_minuteswest; /* minutes west of Greenwich */
    int tz_dsttime;     /* type of DST correction */
}
```

## gettimeofday() vs time()

- time System Call 과 `같은 점`
  - `1970-01-01 00:00` 부터 현재 시간 값을 얻음

- time System Call 과 `다른 점`
  - us 단위로 정밀한 현재 시간을 얻을 수 있다

## 실습 : 현재 시간 출력

- 유튜브 시간상 생략

## 정리 : 시간 측정 방법

초(s) 단위 현재 시간 측정
- sleep 시스템콜 사용

마이크로초(us) 단위 현재 시간 측정
- gettimeofday 시스템콜 사용

# Watchdog Timer

## Watchdog Timer (WDT)

임베디드 시스템에서 오작동을 막기 위한 타이머
- Timer 가 끝나면 시스템 리셋 or 중지 / 메모리 덤프 / 코어 덤프 동작이 이루어진다.
- 평소에는 지속적으로 갱신이 필요하다.

- 동작 중 무한루프가 돌거나 응답이 없을 때 WDT가 Timeout 된다.
  - 임베디드의 안전장치
  - 디버깅에 좋음

## alarm API

`alarm(초)`
- 특정 시간 후 `SIGALRM` Signal이 발생한다 
- alarm 을 다시 울리면 갱신된다

```c
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>
#include <unisted.h>

void gogo() {
    printf("WAKE UP!\n");
    exit(1);
}

int main() {
    signal(SIGALRM, gogo);

    alarm(3);

    printf("3 seconds\n");
    printf("wait...\n");

    while(1) sleep(1);

    return 0;
}

```

> 오늘하는 수업은 안들어도 시험 치는데 지장이 없다 함