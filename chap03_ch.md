# 3.1

假設對於 i = 2、5、9，a[i] == v。此時，

- 首先，對 i = 2，經由 `found_id = i`的處理，found_id = 2。
- 接下來，對 i = 5，經由 `found_id = i` 的處理，found_id = 5。
- 最後，對 i = 9，經由 `found_id = i` 的處理，found_id = 9。

如此，每次找到滿足 a[i] == v 的 i 時，found_id 的值都會被更新。因此，最後 found_id 的值會是滿足 a[i] = v 的 i 之中的最大值。


　

# 3.2

可以使用以下的實作來求得。準備一個變數，在每次找到符合 a[i] == v 的 i  時，將變數增加。計算複雜度為 O(N)。

　

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // 接收輸入
    int N, v;
    cin >> N >> v;
    vector<int> a(N);
    for (int i = 0; i < N; ++i) cin >> a[i];

    // 線性搜尋
    int counter = 0;
    for (int i = 0; i < N; ++i) {
        if (a[i] == v) {
            ++counter;  // 增加計數器
        }
    }

    // 輸出結果
    cout << counter << endl;
}
```



　

# 3.3

可以準備以下 2 個變數。

- 用於表示最小值的變數  `worst_value`
- 用於表示第 2 小值的變數 `second_value`

可以使用這些變數並如下實作。計算複雜度為 O(N)。

　

```cpp
#include <iostream>
#include <vector>
using namespace std;
const int INF = 20000000;   // 設成十分大的值

int main() {
    // 接收輸入
    int N;
    cin >> N;
    vector<int> a(N);
    for (int i = 0; i < N; ++i) cin >> a[i];

    // 線性搜尋
    int worst_value = INF;
    int second_value = INF;
    for (int i = 0; i < N; ++i) {
        if (a[i] < worst_value) {
            second_value = worst_value;
            worst_value = a[i];
        }
        else if (a[i] < second_value) second_value = a[i];
    }

    // 輸出結果
    cout << second_value << endl;
}
```



　

# 3.4

N 個の値の「最大値」と「最小値」を求めます。それらの差が求める値となります。計算量は O(N) です。
找出 N 個值的「最大值」和「最小值」。其差值即為所求的值。計算複雜度為 O(N)。

　

```cpp
#include <iostream>
#include <vector>
using namespace std;
const int INF = 20000000;   // 設成十分大的值

int main() {
    // 接收輸入
    int N;
    cin >> N;
    vector<int> a(N);
    for (int i = 0; i < N; ++i) cin >> a[i];

    // 線性搜尋
    int max_value = -INF;
    int min_value = INF;
    for (int i = 0; i < N; ++i) {
        if (a[i] < min_value) min_value = a[i];
        if (a[i] > max_value) max_value = a[i];
    }

    // 輸出結果
    cout << max_value - min_value << endl;
}
```



　

# 3.5 (ABC 081 B - Shift Only)

例如，思考 a = (8, 12, 40) 的狀況，

- 8 可以被 2 整除 3 次
- 12 可以被 2 整除 2 次
- 40 可以被 2 整除 3 次

可知這之中最小次數的 12 (2 次) 為全體的瓶頸。也就是說，可操作次數為 2 次。根據此事實，思考以下的解法。

------

對 N 個整數 a[0], a[1], …, a[N-1]分別求出「可以被 2 整除多少次」。這些次數中的最小值就是答案。

-----

　

```cpp
#include <iostream>
#include <vector>
using namespace std;

// N 可以被 2 整除多少次
int how_many_times(int N) {
    int exp = 0;
    while (N % 2 == 0) N /= 2, ++exp;
    return exp;
}

// 表示無限大的值
const int INF = 1234567890;

int main() {
    int N;
    cin >> N;
    vector<int> A(N);
    for (int i = 0; i < N; ++i) cin >> A[i];

    // 求出可以被 2 整除多少次的最小值
    int result = INF;
    for (auto a : A) {
        result = min(result, how_many_times(a));
    }
    cout << result << endl;
}
```



　

# 3.6 (ABC 051 B - Sum of Three Integers)

最初可能會想到如下的全搜尋解法。

　

```cpp
int counter = 0;
for (int X = 0; X <= K; ++X) {
    for (int Y = 0; Y <= K; ++Y) {
        for (int Z = 0; Z <= K; ++Z) {
            if (X + Y + Z == N) ++counter;
        }
    }
}
```

　

然而，如果就這樣進行的話，計算複雜度將變為 O(K^3)。因此，我們應該著眼於關係式 X + Y + Z = N。其實，當決定了 X 和 Y 這 2 個值時，如下所示，Z的值只有一種。



Z = N - X - Y



這個值不一定滿足 0 <= Z <= K，但至少一定滿足 Z = N - X - Y。然後，檢查是否滿足 0 <= Z <= K。如果滿足，就將用來表示個數的變數 `counter` 增加。

以上をまとめて、次のように実装できます。なお、添字 X のとる範囲を「X <= K」から「X <= min(K, N)」へと改良しています (添字 Y も同様)。計算量は、M = min(K, N) として、O(M^2) となります。
綜上所述，可以實作如下。另外，將索引 X 的取值範圍從 「X <= K」改進為 「X <= min(K, N)」（索引 Y 也一樣）。設 M = min(K, N)，則計算複雜度為 O(M^2)。

　

```cpp
#include <iostream>
using namespace std;

int main() {
    int K, N;
    cin >> K >> N;

    int count = 0;
    for (int x = 0; x <= min(K, N); ++x) {
        for (int y = 0; y <= min(K, N); ++y) {
            int z = N - x - y;
            if (z >= 0 && z <= K) {
                ++count;
            }
        }
    }
    cout << count << endl;
}
```



　

# 3.7 (ABC 045 C - 許多數式)

我們將活用第 3.5 節所解說的「整數的二進制表示和位元運算」。一個長度為 N 的字串 S 中有 N-1 個「間隙」。對於每個間隙，有兩個選擇：

+ 插入「+」
+ 不插入「+」

因此，總共有 2^(N-1) 種選擇。與「部分和問題」相同，用 0 以上未滿 2^(N-1)-1 的整數值（變數 bit）來管理這些選擇。

雖然可能有點複雜，但可以實作如下。計算複雜度為 O(N2^N)。

　

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string S;
    cin >> S;
    int N = S.size();

    // 嘗試是否要在 N-1 個間隙中插入「+」的所有情況
    long long res = 0;
    for (int bit = 0; bit < (1<<(N-1)); ++bit) {
        // tmp：表示「+」與「+」之間的値的變數
        long long tmp = 0;
        for (int i = 0; i < N-1; ++i) {
            tmp *= 10;
            tmp += S[i] - '0';

            // 插入「+」時，將 tmp 加算到答案中，將 tmp 初始化為 0
            if (bit & (1<<i)) {
                res += tmp;
                tmp = 0;
            }
        }

        // 將最後的「+」之後的剩餘部分加算到答案中
        tmp *= 10;
        tmp += S.back() - '0';
        res += tmp;
    }
    cout << res << endl;
}
```









