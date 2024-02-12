# 스레드를 통해 코어에게 일 시키기

코어에게 일을 시켜보자.

각 Core는 각각의 Thread 처리를 담당한다.

```c
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

pthread_t t[4];

void *abc()
{
    while (1)
        ;
    return 0;
}

int main()
{
    for (int i = 0; i < 4; i++)
    {
        pthread_create(&t[i], NULL, abc, NULL);
        sleep(3);
    }

    for (int i = 0; i < 4; i++)
    {
        pthread_join(t[i], NULL);
    }

    return 0;
}
```

## 결론

Core 개수 만큼 Thread를 만들어 개발 시 CPU의 최고 효율을 낼 수 있다.

# Data Decomposition 이란

- 각 데이터 단위로 분해하여 Thread Programming
- 각 Thread 마다 같은 작업을 수행한다.

=> Core의 최고 효율을 낼 수 있다.

# 카이사르 암호 실습

나의 코드:

```c
#include <stdio.h>
#include <pthread.h>

pthread_t t[4];

char vect[4][21] =
    {
        "ABCDEFGHIJKLMNOPQRST",
        "HIFAKERHIFAKERHIFAKE",
        "BBQBBQBBQBBQBBQBBQBB",
        "MACMACMACMACMACMACMA"};

void *caesar(int num)
{
    for (int i = 0; i < 21; i++)
    {
        vect[num][i] += 3;
    }
}

int main()
{
    for (int i = 0; i < 4; i++)
    {
        pthread_create(&t[i], NULL, caesar(i), NULL);
    }

    for (int i = 0; i < 4; i++)
    {
        pthread_join(t[i], NULL); // 왜 join은 t[i] 지?
    }

    for (int i = 0; i < 4; i++)
    {
        printf("%s\n", vect[i]);
    }

    return 0;
}
```

강사님 코드

```c
#include <stdio.h>
#include <pthread.h>

char vect[4][21] = {
        "ABCDEFGHIJKLMNOPQRST",
        "HIFAKERHIFAKERHIFAKE",
        "BBQBBQBBQBBQBBQBBQBB",
        "MACMACMACMACMACMACMA"
    };

void change(char *str) {
    for (int i = 0; str[i]; i++) {
        char ch = str[i];
        if (ch >= 'A' && ch <= 'Z') {
            ch = ch+3;
        }
        if (ch > 'Z') {
            ch = ch - 26;
        }
        str[i] = ch;
    }
}

void* run(void *p) {
    int line = *(int *)p;
    change(vect[line]);
    return 0;
}

int main() {
    pthread_t t[4];
    int id[4];

    for (int i = 0; i < 4; i++) {
        int id[4];
        pthread_create(&t[i], NULL, run, &id[i]);
    }

    for (int i = 0; i < 4; i++) {
        pthread_join(t[i], NULL);
    }

    for (int y = 0; y < 4; y++) {
        for (int x = 0; x < 20; x++) {
            printf("%c", vect[y][x]);
        }
        printf("\n");
    }
}

```