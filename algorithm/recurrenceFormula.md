# 점화식
> 점화식은 어떤 함수를 자신과 똑같은 함수를 이용해 나타내는 것이다. 자기 호출을 사용하는 함수(재귀 함수)의 복잡도를 구하는 데 유용하다.

## 🔎예시 1) Factorial : n!
```java
int factorial(int n)
{
    if (n == 0) // base case
        return 1;
    else // recursive case
        return n * factorial(n - 1);
}
```
### 1. 수행시간 분석
> T(n) = T(n - 1) + C  