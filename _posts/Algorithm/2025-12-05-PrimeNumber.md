---
title: C++ 알고리즘) 소수
date: 2025-12-04 14:18:00 +0900
categories: ['Algorithm']
tags: ['Algorithm', 'Prime Number', '알고리즘', '소수' ]
math: true
---

> 개인적으로 공부한 내용을 복습 겸 강좌 형식으로 작성한 글입니다. 잘못된 정보, 오타 등이 포함되어 있을 수 있습니다.
{: .prompt-warning }

## 서론

알고리즘 문제를 풀다 보면 정말 질리도록 등장하는 녀석이 있습니다. 바로, **소수**입니다.

"어떤 수가 소수인지 판단하라", "주어진 범위의 소수를 다 출력하라" 등 수학 문제 같지만 알고리즘의 필수 기본기로 들어가기도 합니다.

일상에서 떠올리기 쉽지 않지만,,, 굳이 예를 들자면...

- **암호학**: RSA는 거대한 소수의 곱을 기반으로 강력한 암호를 만들어냅니다.
- **난수 생성**: 소수를 기반으로 만들어지는 다양한 난수 생성 알고리즘이 존재합니다.
- **매미**: 자연에서 매미가 5년, 17년 주기 등으로 우는 이유가 소수와 관련이 있다고 합니다.

이번 글에선 가장 기본적인 소수 판정부터, 시간을 아껴주는 에라토스테네스의 체까지 차근차근 살펴보겠습니다.

----------

## 💡 소수

소수<sup style="color:gray">Prime Number</sup>는 <mark class="dynamic-highlight">1보다 큰 자연수 중에서 1과 자기자신만을 약수로 가지는 수</mark>를 의미합니다. 즉, 다른 어떤 수로도 나누어 떨어지지 않는 그런 찐따 같은 수입니다.

예를 들어, $17$은 소수입니다. 왜냐하면 $2, 3, 4, ..., 16$ 중 그 어떤 것도 $17$을 나누지 못합니다. 반대로, $15$는 $3$과 $5$로 나누어 떨어지니 소수가 될 수 없습니다.

> **1은 소수가 아닙니다.**
>
> 소수의 정의는 **1보다 큰 자연수 중**에서 시작합니다. 따라서 1은 소수도, 합성수도 아닌 특별한 자연수로 분류됩니다.
{: .prompt-warning }

### 단순한 로직

자, 여러분이 RPG 게임에 **소수 레벨**이라는 시스템을 도입한다고 가정해봅시다. 즉, 자신의 캐릭터 레벨이 소수 레벨이 되었을 때 보너스 공격력을 제공하는 겁니다. 아이디어는 좋지만, 캐릭터의 레벨이 소수라는 걸 판단해야 합니다. 어떻게 할까요?

걱정마세요. 일단 아래의 단계를 따라봅시다.

1.  $N$이 $1$ 이하라면 소수가 아니다.
2.  $2$부터 $N - 1$까지 모든 수로 나누어 본다.
3.  만약, 하나라도 나누어 떨어지는 수가 있다면 소수가 아니다.

```cpp
#include <iostream>

bool isPrimeNumber(int num) {
    // 1) 1이하는 소수가 아님
    if (num <= 1) return false;

    // 2) 2부터 N - 1까지 순회
    for (int i = 2; i < N; ++i) {
        // 2-1) 소수가 아님
        if (num % i == 0) return false;
    }

    // 3) 소수
    return true;
}

int main(int argc, char* argv[]) {
    std::ios_base::sync_with_stdio(false);
    std::cin.tie(nullptr);
    std::cout.tie(nullptr);

    // 캐릭터의 레벨
    int level = 17;

    if (isPrimeNumber(level)) {
        std::cout << "캐릭터의 레벨이 소수입니다. 보너스 공격력을 제공합니다!\n";
    } else {
        std::cout << "캐릭터의 레벨이 소수가 아닙니다. 보너스 공격력 제공을 취소합니다.\n";
    }

    return 0;
}
```
<samp>캐릭터의 레벨이 소수입니다. 보너스 공격력을 제공합니다.</samp>

어떤가요? 확실하게 소수를 판별합니다.

하지만, 캐릭터의 레벨 범위가 겁나게 크다고 생각해봅시다. 만약, 10억이라면? 반복문을 10억 번이나 돌아야합니다.

시간복잡도가 $O(N)$으로 꽤 비효율적입니다.

### 루트까지만 반복해보자

수학적인 성질을 조금 이용하면 반복 횟수를 꽤 줄일 수 있습니다.

예를 들어, $36$의 약수를 생각해봅시다.

> (1, 36), (2, 18), (3, 12), (4, 9), (6, 6)

잘 보시면 약수들은 대칭을 이루고 있고, 짝 중 하나는 반드시 $\sqrt{36} = 6$ 이하에서 등장합니다. 즉, 7 이상을 볼 필요가 없다는 겁니다.

따라서, $\sqrt{N}$까지만 검사해서 나누어 떨어지는 지 확인하면 그 이후는 굳이 검사할 필요가 없다는 겁니다.

```cpp
#include <iostream>

bool isPrimeNumber(int num) {
    // 1) 1이하는 소수가 아님
    if (num <= 1) return false;

    // 2) (i * i) <= num은 i <= std::sqrt(num)과 같음
    for (int i = 2; (i * i) <= num; ++i) {
        // 2-1) 소수가 아님
        if (n % i == 0) return false;
    }

    // 3) 소수
    return true;
}

int main(int argc, char* argv[]) {
    std::ios_base::sync_with_stdio(false);
    std::cin.tie(nullptr);
    std::cout.tie(nullptr);

    // 캐릭터의 레벨
    int level = 17;

    if (isPrimeNumber(level)) {
        std::cout << "캐릭터의 레벨이 소수입니다. 보너스 공격력을 제공합니다!\n";
    } else {
        std::cout << "캐릭터의 레벨이 소수가 아닙니다. 보너스 공격력 제공을 취소합니다.\n";
    }

    return 0;
}
```
<samp>캐릭터의 레벨이 소수입니다. 보너스 공격력을 제공합니다.</samp>

`isPrimeNumber` 함수의 내부 로직을 잘 보시면 아래와 같은 반복문이 있습니다.

```cpp
for (int i = 2; (i * i) <= num; ++i) {
    if (n % i == 0) return false;
}
```

`std::sqrt()` 함수 대신 `(i * i) <= num`이라는 정수 연산으로 대체하고 있습니다. 왜냐하면, `std::sqrt()` 함수는 결괏값으로 실수를 반환하기 때문입니다. 실수 연산은 정수 연산보다 훨씬 많은 연산량을 필요로 하죠. 게다가 오차 범위에 대한 문제도 있기 때문에 함수 대신 정수 연산을 대체로 사용합니다.

| 단계 | `i`의 값 | `i * i`의 값 | 조건식                | 연산            | 결과                     |
| :--- | :------- | :----------- | :-------------------- | :-------------- | :----------------------- |
| 1    | 2        | 4            | $4 \le 17$ = `true`   | $17 \% 2 \ne 0$ | 통과                     |
| 2    | 3        | 9            | $9 \le 17$ = `true`   | $17 \% 3 \ne 0$ | 통과                     |
| 3    | 4        | 16           | $16 \le 17$ = `true`  | $17 \% 4 \ne 0$ | 통과                     |
| 4    | 5        | 25           | $25 \le 17$ = `false` |                 | 조건식 통과 안 되어 종료 |

단순한 로직과 비교했을 때 어떤가요? 이 방법의 시간복잡도는 $O(\sqrt{N})$입니다. N이 10억이었을 때 단순 로직은 10억 번을 돌아야 하지만, 이번 방법은 약 31,622번만 계산하면 됩니다. 엄청난 차이죠?

### 에라토스테네스의 체

만약 문제에서 "숫자 하나를 줄테니 소수인지 맞춰봐", "1부터 1,000,000까지의 수 중에서 소수를 모두 출력해"라고 한다면 어떨까요?

음... 우리가 배운 루트까지만 반복하는 방법을 써도 충분하지만, 더 빠른 방법이 있습니다. 바로, 고대 그리스의 수학자 에라토스테네스가 만든 체(Sieve)를 사용하는 것입니다.

원리는 정말 간단합니다. 마치 체로 거르듯이(?) 소수가 아닌 수(배수)들을 지워나가는 겁니다.

1.  $2$부터 시작하여, $2$를 제외한 $2$의 배수를 모두 제거합니다.
2.  다음 남은 수 중 가장 작은 수($3$)를 찾습니다. $3$을 제외한 모든 $3$의 배수를 제거합니다.
3.  다음 남은 수 중 가장 작은 수($5$)를 찾습니다. $4$는 이미 $2$의 배수라 지워진 상태입니다. $5$를 제외한 모든 $5$의 배수를 제거합니다.
4.  반복...

```cpp
#include <iostream>
#include <vector>

int main(int argc, char* argv[]) {
    std::ios_base::sync_with_stdio(false);
    std::cin.tie(nullptr);
    std::cout.tie(nullptr);

    // 1부터 N까지의 소수 구하기
    int N = 100;

    // 초기에는 모든 수가 소수라고 가정
    std::vector<bool> isPrime(N + 1, true);

    // 단, 0과 1은 소수가 아니기에 false로
    isPrime[0] = false;
    isPrime[1] = false;

    // 에라토스테네스의 체
    for (int i = 2; i*i <= N; ++i) {
        // 소수가 아니라면 생략
        if (!isPrime[i]) continue;

        // 배수(소수가 아닌 수)를 false로
        for (int j = i*i; j <= N; j += i) {
            isPrime[j] = false;
        }
    }

    // 결과
    for (int i = 0; i <= N; ++i) {
        if (isPrime[i]) std::cout << i << ", ";
    }
    std::cout << '\n';

    return 0;
}
```
<samp>2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97, </samp>

에라토스테네스의 체 로직을 잘 보시면 이해가 안 가는 부분이 있습니다. 바로 `j = i*i`입니다.

배수를 지우는데 왜 `j = i * 2`가 아닌 `j = i*i`일까요?

$i = 5$일 때 5의 배수를 지운다고 가정해 봅시다. $5 \times 2$는 이미 $i = 2$일 때 지워졌고, $5 \times 3$은 $i = 3$일 때 지워졌습니다. 즉, 아직 지워지지 않은 가장 작은 5의 배수는 $5 \times 5$가 됩니다. 일종의 최적화죠.

----------

## 시간복잡도

1.  **단순 반복**
    
    $O(N)$으로, 매우 느리기 때문에 잘 사용하지 않습니다.
2.  **루트까지 반복**
    
    $O(\sqrt{N})$으로, 단일 숫자의 소수를 판별할 때 주로 사용합니다.
3.  **에라토스테네스의 체**
    
    $O(N \log (\log N))$으로, 거의 $O(N)$에 가깝게 동작합니다. 대량의 소수를 판별할 때 사용합니다.

----------

## 마무리

1은 소수가 아닙니다. 그래서 반드시 예외 처리를 해야합니다.

소수의 경우 별도의 함수를 제공하지 않기 때문에 에라토스테네스의 체의 구현을 **반드시** 익혀두실 필요가 있습니다.

----------

## 추천 문제
### [⚔️ 백준 1978번 - 소수 찾기](https://www.acmicpc.net/problem/1978 "백준 1978번 - 소수 찾기")

<details>

```cpp
#include <iostream>

bool isPrimeNumber(int num) {
    if (num <= 1) return false;

    for (int i = 2; i*i <= num; ++i) {
        if (num % i == 0) return false;
    }

    return true;
}

int main(int argc, char* argv[]) {
    std::ios_base::sync_with_stdio(false);
    std::cin.tie(nullptr);
    std::cout.tie(nullptr);

    // N
    int N; std::cin >> N;
    
    int cnt = 0;
    for (int i = 0; i < N; ++i) {
        int x; std::cin >> x;

        if (isPrimeNumber(x)) cnt += 1;
    }

    std::cout << cnt;

    return 0;
}
```

이 문제는 주어지는 수가 1,000 이하이기 때문에 굳이 에라토스테네스의 체를 사용하지 않고도 풀 수 있습니다.

</details>

### [⚔️ 백준 1929번 - 소수 구하기](https://www.acmicpc.net/problem/1929 "백준 1929번 - 소수 구하기")

<details>

```cpp
#include <iostream>
#include <vector>

int main(int argc, char* argv[]) {
    std::ios_base::sync_with_stdio(false);
    std::cin.tie(nullptr);
    std::cout.tie(nullptr);

    // M, N
    int M, N; std::cin >> M >> N;

    // 에라토스테네스의 체
    std::vector<bool> isPrime(1'000'000 + 1, true);

    // 0과 1은 소수가 아님
    isPrime[0] = false;
    isPrime[1] = false;

    // 로직
    for (int i = 2; i*i <= 1'000'000; ++i) {
        // 소수가 아님
        if (!isPrime[i]) continue;

        // 배수(소수가 아님)
        for (int j = i*i; j <= 1'000'000; j += i) {
            isPrime[j] = false;
        }
    }

    // 답
    for (int i = M; i <= N; ++i) {
        if (isPrime[i]) {
            std::cout << i << '\n';
        }
    }

    return 0;
}
```

이 문제는 에라토스테네스의 체를 이용해 해결할 수 있습니다.

</details>

### [⚔️ 백준 2581번 - 소수](https://www.acmicpc.net/problem/2581 "백준 2581번 - 소수")

<details>

```cpp
#include <iostream>
#include <vector>

bool isPrimeNumber(int num) {
    if (num <= 1) return false;

    for (int i = 2; i*i <= num; ++i) {
        if (num % i == 0) return false;
    }

    return true;
}

int main(int argc, char* argv[]) {
    std::ios_base::sync_with_stdio(false);
    std::cin.tie(nullptr);
    std::cout.tie(nullptr);

    // M, N
    int M; std::cin >> M;
    int N; std::cin >> N;

    int sum = 0;                        // 소수의 합
    int min = 1'000'000'000;            // 최솟값

    for (int i = M; i <= N; ++i) {
        if (isPrimeNumber(i)) {
            sum += i;

            if (i < min) min = i;
        }
    }

    if (sum == 0 && min == 1'000'000'000) std::cout << -1;
    else std::cout << sum << '\n' << min;

    return 0;
}
```

수의 범위가 10,000으로, 에라토스테네스의 체 사용없이 쉽게 해결할 수 있습니다.

</details>

### [⚔️ 백준 2960번 - 에라토스테네스의 체](https://www.acmicpc.net/problem/2581 "백준 2960번 - 에라토스테네스의 체")

<details>

```cpp
#include <iostream>
#include <vector>

int main(int argc, char* argv[]) {
    std::ios_base::sync_with_stdio(false);
    std::cin.tie(nullptr);
    std::cout.tie(nullptr);

    // N, K
    int N, K;
    std::cin >> N >> K;

    // 에라토스테네스의 체
    std::vector<bool> isPrime(N + 1, true);
    isPrime[0] = false;
    isPrime[1] = false;

    int cnt = 0;
    for (int i = 2; i <= N; ++i) {
        for (int j = i; j <= N; j += i) {
            if (!isPrime[j]) continue;

            isPrime[j] = false;
            cnt += 1;

            if (cnt == K) {
                std::cout << j;
                goto end;
            }
        }
    }

end:
    return 0;
}
```

에라토스테네스의 체에서 조금 응용하면 되는 문제입니다.

이 문제는 에라토스테네스의 체 알고리즘을 요구하긴 하지만, 지워지는 순서를 그대로 찾아야하기 때문에 `i*i`를 사용해선 안 됩니다.

</details>

### [⚔️ 백준 4948번 - 베르트랑 공준](https://www.acmicpc.net/problem/4948 "백준 4948번 - 베르트랑 공준")

<details>

```cpp
#include <iostream>
#include <vector>

int main(int argc, char* argv[]) {
    std::ios_base::sync_with_stdio(false);
    std::cin.tie(nullptr);
    std::cout.tie(nullptr);

    // 에라토스테네스의 체
    std::vector<bool> isPrime((123'456 * 2) + 1, true);
    isPrime[0] = false;
    isPrime[1] = false;

    for (int i = 2; i*i <= (123'456 * 2); ++i) {
        if (!isPrime[i]) continue;

        for (int j = i*i; j <= (123'456 * 2); j += i) {
            isPrime[j] = false;
        }
    }

    // N
    int N;
    while (std::cin >> N) {
        if (N == 0) break;

        int A = N + 1;
        int B = N * 2;

        int cnt = 0;
        for (int i = A; i <= B; ++i) {
            if (isPrime[i]) cnt += 1;
        }

        std::cout << cnt << '\n';
    }

    return 0;
}
```

에라토스테네스의 체를 이용하면 쉽게 해결할 수 있는 문제입니다.

`N`보다 크고, `2N`보다 작거나 같은 수라는 조건에 주의하세요.

> 이 문제는 더 최적화할 수 있습니다.
>
> 메모리를 더 사용하게 되지만, 누적합을 이용하여 엄청나게 빠른 속도로 답을 구해낼 수 있습니다. 아직 우리가 배운 알고리즘이 아니기 때문에 설명은 생략하지만, 이미 아시는 분은 시도해보시기 바랍니다.
{: .prompt-tip }

</details>