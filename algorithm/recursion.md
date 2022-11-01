# Recursive Thinking 
## ✏️ 순환 함수의 기본
### 1. 문자열의 길이 계산 
```java
int length(char *str) 
{
    if (*str == '/0') // 문자열이 null일 때 
        return 0;
    else 
        return 1 + length(str + 1);
}
```
### 2. 문자열 프린트
```java 
// 문자열 프린트는 다음과 같은 방법으로 할 수 있다.
printf("%s", str);
```

```java
// 하지만 Recursion의 이해를 돕기 위해 재귀적으로 구현해보겠다.
void printChars(char *str)
{
    if (*str == '/0')
        return;
    else {
        printf("%c", *str); // str의 값
        printChars(str + 1); // 주소값 전달
    }
}
```

### 3. 문자열 뒤집어 프린트
```java
void printCharReverse(char *str)
{
    if (*str == '/0')
        return;
    else {
        printCharReverse(str + 1); // 문자열을 뒤집고
        print("%c", *str); // 끝부터 프린트
    }
}
```

### 4. 이진수로 변환하여 출력
```java
// 음이 아닌 정수 n을 이진수로 변환하여 인쇄한다.
void printlnBinary(int n)
{
    if (n < 2) 
        printf("%d", n);
    else {
        // n을 2로 나눈 몫을 먼저 2진수로 변환하여 인쇄한 후
        printlnBinary(n / 2); 
        // n을 2로 나눈 나머지를 인쇄한다.
        printf("%d", n % 2);
    }
}
```

<br>

------------------------------------------------------------------

## 📌 **Recursion vs Iteration**
- 모든 순환함수는 반복문(iteration)으로 변경 가능
- 모든 반복문은 recursion으로 표현 가능함
- 순환함수는 복잡한 알고리즘을 단순하고 알기 쉽게 표현하는 것을 가능하게 함
- 순환함수는 함수 호출에 따른 오버해드가 있을 수 있음  
(매개변수 전달, 액티베이션 프레임 생성 등)
  
<br>

## 📌 **순환적 알고리즘 설계**
- 적어도 하나의 base case가 존재해야 함  
(순환되지 않고 종료되는 case가 있어야 함)
- 모든 case는 결국 base case로 수렴해야 함
- 큰 문제의 solution은 작은 문제의 solution들의 관계로 표현함
<br>

------------------------------------------------------------------
## ✏️ **이진탐색 (Binary Search)**
### 1. Iterative Version
```java
int binarySearch(int data[], int n, int target)
{
    int begin = 0, end = n - 1;
    while(begint <= end) {
        int middle = (begin + end) / 2;
        if (data[middle] == target)
            return middle;
        else if (data[middle] > target)
            end = middle - 1;
        else
            begin = middle + 1;
    }
    
    return -1;
}
```

### 2. Recursion
```java
// data[begin]에서 data[end] 사이에서 target을 검색
int binarySearch(int data[], int target, int begin, int end)
{
    if (begin > end)
         return -1;
    else {
        int middle = (begin + end) / 2;
        if (data[middle] == target)
            return middle;
        else if (data[middle] > target)
            return binarySearch(data, target, begin, middle - 1);
        else
            return binarySearch(data, target, middle + 1, end);
    }
}
```

<br>

------------------------------------------------------------------

## ✏️ 하노이 타워 