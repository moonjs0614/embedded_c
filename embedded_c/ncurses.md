# ncurses 소개

ncurses (엔컬즈스, new curses 의 약어)
- CLI로 GUI 같은 App을 만들고자 할 때 사용하는 Library

## Library 설치 방법

`$sudo apt install libncursesw5-dev`

빌드 테스트

`$gcc ./파일명.c -o ./파일명 -lcursesw && ./파일명`

```c
#include <ncurses.h>

int main()
{
    initscr(); // ncurses 시작을 위한 내부 세팅
    printw("Hello world");
    refresh(); // 화면 갱신 명령어 (printw만 쓰면 화면에 글씨 안 나올 수 있음)

    getch(); // get character

    endwin(); // ncurses 종료를 위한 내부 정리 (ncurses에 할당된 메모리 할당 해제)
    return 0;
}
```

한 줄씩 출력하기

```c
#include <ncurses.h>
#include <unistd.h>

int main()
{
    initscr(); // ncurses 시작을 위한 내부 세팅

    char str[27] = {'\0'};
    char ch = 'A';
    for (int i = 0; i < 26; i++)
    {
        str[i] = ch++;
        printw("%s\n", str);
        refresh();
        sleep(1);
    }
    getch();
    endwin(); // ncurses 종료를 위한 내부 정리 (ncurses에 할당된 메모리 할당 해제)
    return 0;
}
```

A~Z 까지 0.1초에 한 글자씩 출력

```c
#include <ncurses.h>
#include <unistd.h>

int main()
{
    initscr(); // ncurses 시작을 위한 내부 세팅

    char ch = 'A';
    for (int i = 0; i < 26; i++)
    {
        printw("%c", ch++);
        refresh();
        usleep(100 * 1000);
    }
    getch();
    endwin(); // ncurses 종료를 위한 내부 정리 (ncurses에 할당된 메모리 할당 해제)
    return 0;
}

```

# counting 하기

## 짧은 카운팅

빠르게 카운팅하기
- 헤더파일 : unistd.h
- usleep 함수

시간 단위
- sec > ms 밀리초 > us 마이크로초 > ns 나노초

```c
#include <ncurses.h>
#include <unistd.h>

int main()
{
    initscr();

    printw("READY\n");
    refresh();
    usleep(1000 * 200);

    printw("GO\n");
    refresh();
    usleep(100 * 500);

    printw("FINISH\n");
    refresh();

    getch();
    endwin();
    return 0;
}
```

## 카운트 다운

```c
#include <ncurses.h>
#include <unistd.h>

int main()
{
    initscr();

    for (int sec = 0; sec <= 9; sec++)
    {
        for (int cs = 0; cs <= 5; cs++)
        {
            for (int ms = 0; ms <= 9; ms++)
            {
                printw("%d.%d%d", sec, cs, ms);
                refresh();
                clear();
                usleep(10 * 1000);
            }
        }
    }
    printw("10.00");
    getch();
    endwin();
    return 0;
}
```