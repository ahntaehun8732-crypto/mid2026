# 숫자야구 판정 게임 (C언어)

## 1. 문제 설명
- 정답 숫자 3개와 사용자가 입력한 숫자 3개를 비교하여 **스트라이크(Strike)**와 **볼(Ball)** 개수를 판정하는 프로그램입니다.
- 숫자와 위치가 모두 일치하면 Strike, 숫자는 같지만 위치가 다르면 Ball로 판정합니다.
- 3 스트라이크일 경우 "HOMERUN"을 추가로 출력합니다.

## 2. 소스 코드 (`main.c`)

```c
#include <stdio.h>

int main(void) {
    // 정답 숫자와 사용자 입력 배열 (예시 데이터)
    int ans[3] = {1, 2, 3};
    int inp[3];
    
    printf("3개의 숫자를 입력하세요 (예: 1 3 5): ");
    for (int i = 0; i < 3; i++) {
        scanf("%d", &inp[i]);
    }

    int strike = 0;
    int ball = 0;

    // 스트라이크 및 볼 판정 로직
    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {
            if (ans[i] == inp[j]) {
                if (i == j) {
                    strike++; // 값과 위치가 모두 같은 경우
                } else {
                    ball++;   // 값은 같고 위치가 다른 경우
                }
            }
        }
    }

    // 결과 출력
    printf("결과: %d Strike, %d Ball\n", strike, ball);

    // 3 스트라이크인 경우 홈런 출력
    if (strike == 3) {
        printf("HOMERUN!\n");
    }

    return 0;
}
