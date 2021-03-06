# 부분 합(Partial Sum)

* N명의 시험 성적을 내림차순으로 정렬해 둔 배열 scores[]가 있고, a등에서 b등까지의 평균 점수를 계산하는 함수 average(a, b)를 만든다고 하자.
* 일반적인 방법은 scores[a]부터 scores[b]까지를 순회하며 각 수를 더하고 이것을 b - a + 1로 나누는 것이다. 이렇게 하면 반복문의 수행 횟수는 최대 O(N)이 된다.
* 평균 점수를 한 번 계산할 것이라면 이걸로 충분하지만 average()를 여러 번 호출하게 될 거라면 최적화에 대해 생각하게 된다.
* 이럴 때 유용하게 사용되는 것이 부분 합(partial sum) 혹은 누적 합이다.



## 부분 합

* 부분 합이란 배열의 각 위치에 대해 배열의 시작부터 현재 위치까지의 원소의 합을 구해 둔 배열이다.
* scores[]의 부분 합 psum[]의 각 원소를 다음처럼 정의할 수 있다.

$$
psum[i] = \sum_{j=0}^i scores[j]
$$

* 다음 표는 한 예제 배열 scores[]의 각 원소와 해당 배열의 부분 합을 보여준다.

| i      | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    |
| ------ | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| scores | 100  | 97   | 86   | 79   | 66   | 52   | 49   | 42   | 31   |
| psum   | 100  | 197  | 283  | 362  | 428  | 480  | 529  | 571  | 602  |



* psum을 미리 계산해 두면 scores[]의 특정 구간의 합을 O(1)에 구할 수 있다.
* psum[-1] = 0 이라고 가정할 때, scores[a]부터 scores[b]까지의 합을 다음과 같이 얻을 수 있다.

```c++
psum[b] - psum[a - 1]
```



## 부분 합 계산하기

* 구간 합을 빠르게 계산하기 위해서는 부분 합을 미리 계산해 둘 필요가 있다.
* 부분 합을 계산하는 데 드는 시간은 수열의 길이에 따라 선형으로 증가한다.
* 반복문을 통해 구간 합을 구하기 위해 최대 O(N)의 시간이 걸린다는 점에서 구간 합을 두 번 이상 구할 때는 대부분의 경우 부분 합을 사용하는 쪽이 이득이다.
* C++을 사용하는 경우, STL에 포함되어 있는 partial_sum()을 이용할 수 있다.

```c++
// 부분합을 계산하는 함수와 이를 이용해 구간합을 계산하는 함수의 구현
// 주어진 벡터 a의 부분합을 계산한다.
vector<int> partialSum(const vector<int>& a) {
    vector<int> ret(a.size());
    ret[0] = a[0];
    for(int i = 1; i < a.size(); i++)
        ret[i] = ret[i - 1] + a[i];
    return ret;
}
// 어떤 벡터의 부분합 psum[]이 주어질 때, 원래 벡터의 a부터 b까지의 합을 구한다.
int rangeSum(const vector<int>& psum, int a, int b) {
    if(a == 0) return psum[b];
    return psum[b] - psum[a - 1];
}
```



## 부분 합으로 평균 계산하기

* 부분 합 psum을 계산해 두면 원 벡터의 구간 합을 간단하게 구할 수 있다.
* rangeSum()은 특정 구간의 합을 O(1)로 계산해 준다.
* rangeSum()의 결과를 b - a + 1로 나누면 해당 구간의 평균을 쉽게 구할 수 있다.

```c++
rangeSum(psum, a, b) / (b - a + 1)
```



## 부분 합으로 분산 계산하기

* 배열 A[]의 구간 A[a...b]의 분산은 다음과 같은 식으로 정의된다.

$$
v = \frac{1}{b-a+1} \cdot \sum_{i=a}^b (A[i]-m_{a,b})^2
$$

$$
= \frac{1}{b-a+1} \cdot \sum_{i=a}^b (A[i]^2-2A[i] \cdot m_{a,b} + {m_{a,b}}^2)
$$

$$
= \frac{1}{b-a+1} \cdot (\sum_{i=a}^b A[i]^2 - 2m_{a,b} \cdot \sum_{i=a}^b A[i] + (b-a+1){m_{a,b}}^2)
$$

* 이 식에서 m<sub>a,b</sub>는 해당 구간의 평균이다.
* 괄호 안의 세 항 중, 가운데 항과 오른쪽 항은 psum을 이용해 쉽게 계산할 수 있다.
* A[i]<sup>2</sup>의 합 또한 A[]의 각 원소의 제곱의 부분 합을 저장하는 배열을 미리 만들어 두면 O(1)에 계산할 수 있다.

```c++
// 배열의 부분합과 제곱의 부분합을 입력받고 특정 구간의 분산을 계산하는 함수의 구현
// A[]의 제곱의 부분 합 벡터 sqpsum, A[]의 부분 합 벡터 psum이 주어질 때
// A[a..b]의 분산을 반환한다.
double variance(const vector<int>& sqpsum, const vector<int>& psum, int a, int b) {
    // 우선 해당 구간의 평균을 계산한다.
    double mean = rangeSum(psum, a, b) / double(b - a + 1);
    double ret = rangeSum(sqpsum, a, b) - 2 * mean * rangeSum(psum, a, b) + (b - a + 1) * mean * mean;
    return ret / (b - a + 1);
}
```



## 2차원으로의 확장

* 부분 합은 1차원 배열에서만 쓸 수 있는 것은 아니다.
* 2차원 배열 A[]\[]이 주어질 때, A[y<sub>1</sub>, x<sub>1</sub>]에서 A[y<sub>2</sub>, x<sub>2</sub>]까지의 직사각형 구간의 합을 계산해야 한다고 하자.
* 다음과 같은 부분 합 배열을 사용해 이 구간의 합을 빠르게 구할 수 있다.

$$
psum[y,x] = \sum_{i=0}^y \sum_{j=0}^x A[i,j]
$$

* psum[y, x]는 (0, 0)을 왼쪽 위 칸, (y, x)를 오른쪽 아래 칸으로 갖는 직사각형 구간에 포함된 원소들의 합이다.
* psum[]\[]을 미리 계산해 두면 2차원 배열에서도 우리가 원하는 구간의 합을 쉽게 구할 수 있다.

$$
sum(y_1,x_1,y_2,x_2) = psum[y_2,x_2] - psum[y_2,x_1-1] - psum[y_1-1,x_2] + psum[y_1-1,x_1-1]
$$



```c++
// 부분 합을 이용해 2차원 배열의 구간 합을 구하는 함수의 구현
// 어떤 2차원 배열 A[][]의 부분합 psum[][]이 주어질 때,
// A[y1,x1]과 A[y2,x2]를 양 끝으로 갖는 부분 배열의 합을 반환한다.
int gridSum(const vector<vector<int> >& psum, int y1, int x1, int y2, int x2) {
    int ret = psum[y2][x2];
    if(y1 > 0) ret -= psum[y1-1][x2];
    if(x1 > 0) ret -= psum[y2][x1-1];
    if(y1 > 0 && x1 > 0) ret += psum[y1-1][x1-1];
    return ret;
}
```



## 예제: 합이 0에 가까운 구간

* 양수와 음수가 모두 포함된 배열 A[]가 있을 때, 그 합이 0에 가장 가까운 구간을 찾는 문제

| i    | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| A[i] | -14  | 7    | 2    | 3    | -8   | 4    | -6   | 8    | 9    | 11   |

* A[]에는 합이 0인 구간은 없지만, 0에 가장 가까운 구간은 A[2] ~ A[5]로 그 합이 1이다.
* 이런 구간을 찾는 한 가지 방법은 A의 모든 구간을 순회하면서 각각의 합을 계산하는 것이다. 이렇게 하면 배열의 길이 N에 대해 O(N<sup>2</sup>)의 시간이 걸린다.
* 부분 합을 사용하면 이 문제를 아주 쉽게 풀 수 있다.
* 구간 합을 사용하면 A[i] ~ A[j] 구간의 합을 다음과 같이 표현할 수 있다.

$$
\sum_{k=i}^j A[k] = psum[j] - psum[i-1]
$$

* 값이 0에 가깝다는 말은 psum[]의 두 값의 차이가 가장 적다는 뜻이다.
* 주어진 배열에서 가장 가까운 두 값을 찾기 위한 간단한 방법은 이 배열을 정렬한 뒤 인접한 원소들을 확인하는 것이다.
* 정렬은 O(NlgN)시간에 수행할 수 있고, 부분 합을 구하는 것과 인접한 원소들을 확인하는 것 모두 O(N)에 할 수 있으니 이 알고리즘의 수행 시간은 O(NlgN)이 된다.

