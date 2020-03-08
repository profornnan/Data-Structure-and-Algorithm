# 재귀(Recursion)

* 순환 또는 재귀함수라고 부른다.
* 메소드를 정의할 때 자기 자신을 재참조 하는 방법을 말한다.



## 조합(Combination)

* 집합에서 서로 다른 n개의 원소 중에서 순서에 상관없이 r개를 선택하는 것을 **조합**이라고 하며, 그 경우의 수를 **이항계수**라고 한다.

$$
_nC_r = _{n-1}C_{r-1} + _{n-1}C_r
$$

$$
_nC_k = \binom{n}{k}
$$

#### 이항계수

```c++
#include <iostream>
using namespace std;

int d[31][31];

int combination(int n, int r) {
	if (n == r || r == 0)
		return 1;
	if (d[n][r])
		return d[n][r];
	return d[n][r] = combination(n - 1, r - 1) + combination(n - 1, r);
}
```



