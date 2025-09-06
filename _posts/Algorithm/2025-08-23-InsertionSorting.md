---
title: 알고리즘 공부) 삽입 정렬
date: 2025-08-23 22:47:00 +0900
categories: ['Algorithm']
tags: ['Algorithm', 'Sort', '알고리즘', '정렬', 'Insertion', '삽입' ]
math: true
---

> 개인적으로 공부한 내용을 정리하여 작성하였기 때문에 잘못된 정보가 포함되어 있을 수 있습니다.
{: .prompt-warning }

## 삽입 정렬
삽입 정렬은 **삽입**이라는 이름처럼, 데이터를 하나씩 꺼내서 이미 정렬된 구간에 **삽입**하는 방식입니다.

포커와 같은 카드 게임을 진행 중이라고 상상해봅시다. 처음에는 카드가 한 장뿐이니 카드를 보기 좋게 정리할 필요가 없습니다. 두 번째 카드를 받으면, 첫 번째 카드와 비교해서 어떤 카드가 더 작은 지 또는 큰 지 확인한 후 적절한 위치로 카드를 옮깁니다. 만약 첫 카드가 7이고, 두번째 카드가 3이라면 3을 앞으로(좌측) 옮겨서 [ 3, 7 ]처럼 카드를 보기 좋게 정렬할 수 있습니다. 세번째 카드로 5를 받았다고 해봅시다. 이미 정렬된 [ 3, 7 ]과 비교하여 5는 7보다 작기 때문에 좌측으로 이동하고, 3보단 크기 때문에 3과 7 사이에 카드를 끼워 넣습니다. 이후 새 카드를 받았을 때에도 마찬가지입니다.

이 과정에서 이미 정렬된 부분(좌측)은 항상 유지되고, 새 카드를 적절한 자리에 밀어 넣는 방식으로 진행됩니다.

### 기본 원리
1. 배열의 첫 요소는 이미 정렬된 것으로 간주합니다.
2. 두번째 요소부터 시작해서, 각 요소를 키<sup style="color:gray">Key</sup>라고 부릅니다. 이 키를 정렬된 부분(좌측)과 비교합니다.
3. 키가 들어갈 적절한 위치를 찾기 위해, 정렬된 부분을 오른쪽에서 왼쪽으로 스캔하면서 더 큰 요소들을 오른쪽으로 한 칸씩 밀어냅니다.
4. 적절한 위치를 찾으면 키를 삽입합니다.
5. 이 과정을 배열의 끝까지 반복합니다.

이제 **삽입**이라 불리는 이유를 아시겠나요? 키를 뽑아서 기존 정렬된 구간 사이에 끼워 넣기 때문에 그렇습니다. 비교하여 밀어내는 것이 핵심입니다.

### 코드 예시

<div class="code-tabs" markdown="0">
    <div class="code-tab active" onclick="showCode('cpp')">C++</div>
    <div class="code-tab" onclick="showCode('java')">JAVA</div>
</div>

{::options parse_block_html="true" /}
<div id="code-cpp" class="code-block active">
```cpp
#include <iostream>

/// @brief 삽입 정렬
/// @param arr 배열
/// @param length 배열의 길이
void InsertionSort(int* arr, int length) noexcept {
    if (arr == nullptr || length == 0) { return; }

    // 1) 배열의 첫 요소는 이미 정렬된 것으로 간주하기 때문에 i는 1부터 시작한다.
    for (int i = 1; i < length; ++i) {
        int key = arr[i];       // 현재 키(삽입 대상)
        int j = i - 1;          // 정렬된 부분의 마지막 인덱스

        // 2) 키가 들어갈 위치 찾기
        // 정렬된 부분을 우측에서 좌측순으로 이동 및 비교하며 밀어낸다.
        while (j >= 0 && arr[j] > key) {    // j가 0 이상이고, arr[j]가 key보다 크면
            arr[j + 1] = arr[j];            // 우측으로 한 칸 밀어낸다.
            --j;                            // 왼쪽으로 이동
        }

        // 3) 적절한 위치에 키 삽입(끼워 넣기)
        arr[j + 1] = key;
    }
}

int main(int argc, char* argv[]) {
    int arr[] = { 5, 3, 8, 4, 2 };

    // 삽입 정렬 수행
    InsertionSort(arr, sizeof(arr) / sizeof(arr[0]));

    // 결과 출력
    for (int num : arr) {
        std::cout << num << " ";
    }
    std::cout << "\n";

    return 0;
}
```
</div>

{::options parse_block_html="true" /}
<div id="code-java" class="code-block">
```java
public class Main {
    /**
     * @brief 삽입 정렬
     * @param arr 배열
     */ 
    static void InsertionSort(int[] arr) {
        if (arr.length == 0) { return; }

        // 1) 배열의 첫 요소는 이미 정렬된 것으로 간주하기 때문에 i는 1부터 시작한다.
        for (int i = 1; i < arr.length; ++i) {
            int key = arr[i];       // 현재 키(삽입 대상)
            int j = i - 1;          // 정렬된 부분의 마지막 인덱스

            // 2) 키가 들어갈 위치 찾기
            // 정렬된 부분을 우측에서 좌측순으로 이동 및 비교하며 밀어낸다.
            while (j >= 0 && arr[j] > key) {    // j가 0 이상이고, arr[j]가 key보다 크면
                arr[j + 1] = arr[j];            // 우측으로 한 칸 밀어낸다.
                --j;                            // 왼쪽으로 이동
            }

            // 3) 적절한 위치에 키 삽입(끼워 넣기)
            arr[j + 1] = key;
        }
    }
    
    public static void main(String[] args) {
        int[] arr = new int[] { 5, 3, 8, 4, 2 };

        // 삽입 정렬
        InsertionSort(arr);

        // 결과 출력
        for (int num : arr) {
            System.out.print(num + " ");
        }
        System.out.println();
    }
}
```
</div>

> 내림차순으로 정렬하려면 `arr[j] > key`를 `arr[j] < key`로 변경하면 됩니다.
{: .prompt-tip }

### 시간 복잡도와 성능
최선의 경우 이미 정렬된 배열이면 각 키를 한 번만 비교하고 끝나기 때문에 $O(N)$입니다. 하지만, 최악의 경우 역순으로 된 배열... 즉 내림차순으로 되어 있을 경우 각 키가 맨 앞으로 가야하기 때문에 비교와 이동 연산이 많아 $O(N^2)$입니다. 평균적으론 $O(N^2)$입니다.

삽입 정렬은 추가 메모리 공간을 필요로 하지 않고 배열 안에서만 연산을 수행하기 때문에 $O(1)$입니다.

### 장점과 단점
#### 장점
비교 기반 정렬 알고리즘 중에서 간단하고 이해하기 쉬운 편입니다. 그리고 코드의 길이가 짧습니다.

같은 값의 순서가 유지되기 때문에 안정적(Stable)입니다.

#### 단점
데이터의 양이 많을 경우 속도가 많이 느립니다. 비교와 이동 연산이 많아지기 때문에 버블 정렬처럼 비효율적일 수 있습니다.

## 마무리
삽입 정렬은 비교 기반 정렬 알고리즘 중에서 직관적이고 단순합니다. 구현이 간단한만큼 작은 데이터에서 빠르고 안정적입니다. 하지만, 큰 데이터에선 매우 느리기 때문에 효율적인 방법을 고려해야 합니다.