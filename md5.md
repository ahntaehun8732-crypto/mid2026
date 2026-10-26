# C언어 알고리즘의 빅오(Big-O) 기반 속도 비교

## 1. 빅오(Big-O) 표기법이란?
- 알고리즘의 **시간 복잡도(Time Complexity)**를 나타내는 지표로, 입력 크기 $N$이 증가함에 따라 연산 횟수가 증가하는 추세를 상한(Worst-case) 기준으로 나타낸 것입니다.

---

## 2. 주요 빅오 표기법 속도 비교 (빠른 순서)

$$\mathcal{O}(1) < \mathcal{O}(\log N) < \mathcal{O}(N) < \mathcal{O}(N \log N) < \mathcal{O}(N^2) < \mathcal{O}(2^N)$$

### ① $\mathcal{O}(1)$ - 상수 시간 (Constant Time)
- 입력 크기 $N$과 상관없이 항상 일정한 연산 횟수를 가집니다.
- **C언어 예시**: 배열의 인덱스로 값에 접근하는 것 (`arr[i]`)

### ② $\mathcal{O}(\log N)$ - 로그 시간 (Logarithmic Time)
- 데이터가 커질 때마다 연산 횟수가 절반씩 줄어드는 형태입니다.
- **C언어 예시**: 이진 탐색 (`bsearch`, Binary Search)

### ③ $\mathcal{O}(N)$ - 선형 시간 (Linear Time)
- 입력 크기 $N$에 비례하여 연산 횟수가 증가합니다.
- **C언어 예시**: 단일 `for` 문을 이용한 순차 탐색(Sequential Search)

### ④ $\mathcal{O}(N \log N)$ - 선형 로그 시간 (Linearithmic Time)
- 효율적인 정렬 알고리즘들이 가지는 복잡도입니다.
- **C언어 예시**: 표준 라이브러리 정렬 함수 `qsort()` (퀵정렬 평균/최선), 병합 정렬(Merge Sort)

### ⑤ $\mathcal{O}(N^2)$ - 이차 시간 (Quadratic Time)
- 입력 크기의 제곱에 비례하며, 주로 이중 반복문에서 발생합니다.
- **C언어 예시**: 버블 정렬(Bubble Sort), 선택 정렬(Selection Sort), 삽입 정렬(Insertion Sort), 숫자야구 이중 판정 로직

---

## 3. 요약 및 결론
- C언어 프로그래밍 시 대용량 데이터를 다룰 때 느린 $\mathcal{O}(N^2)$ 알고리즘 대신 $\mathcal{O}(N \log N)$의 `qsort()`나 $\mathcal{O}(\log N)$의 이진 탐색을 활용하는 것이 성능 최적화의 핵심입니다.
