# 5.1 (EDPC C - Vacation)

如果沒有「不能連續2 天選擇相同類型」的限制，則簡單的將各個日程的 `max(a[i], b[i], c[i])` 加總即可。但由於實際上有限制，因此需要考慮符合該限制的解法。

讓我們定義一個陣列 `dp` ，如下所示。

-----

`dp[i][j]` ← 可以當成有關前 i 天之間的行動。假設在這 i 天的行動中，最後一天的活動當 j = 0 時是「海裡游泳」， j = 1 時是「捉蟲」， j = 2 時是「作業」。那時的幸福度的最大值

-----

然後，關於從 `dp[i]` 轉移到 `dp[i+1]` ，共有以下 6 種模式：

- 從 `dp[i][0]` 轉移到 `dp[i+1][1]`
- 從 `dp[i][0]` 轉移到 `dp[i+1][2]`
- 從 `dp[i][1]` 轉移到 `dp[i+1][0]`
- 從 `dp[i][1]` 轉移到 `dp[i+1][2]` 
- 從 `dp[i][2]` 轉移到 `dp[i+1][0]`
- 從 `dp[i][2]` 轉移到 `dp[i+1][1]`

可以進行如下處理。由於將 6 條式子並列寫出很不方便，所以使用索引 j 、 k 且重複 for 敘述迴圈的方式來進行。 

-----

對各 (j, k) (j, k = 0, 1, 2, j != k) ，

`chmax(dp[i+1][k], dp[i][j] + a[i][k]);`

-----

綜上所述，可以實作如下。計算複雜度為 O(N) 。

　

```cpp
#include <iostream>
#include <vector>
using namespace std;
template<class T> void chmax(T& a, T b) { if (a < b) a = b; }
template<class T> void chmin(T& a, T b) { if (a > b) a = b; }

int main() {
    // 輸入
    int N; 
    cin >> N;
    vector<vector<long long>> a(N, vector<long long>(3));
    for (int i = 0; i < N; ++i) 
        for (int j = 0; j < 3; ++j) 
            cin >> a[i][j];

    // DP 表
    vector<vector<long long>> dp(N+1, vector<long long>(3, 0));

    // 從第 i 天到第 i+1 天
    for (int i = 0; i < N; ++i) {
        // 第 i 天的狀態為 j、第 i+1 天的狀態為 k
        for (int j = 0; j < 3; ++j) {
            for (int k = 0; k < 3; ++k) {
                if (j == k) continue;
                chmax(dp[i + 1][k], dp[i][j] + a[i][k]);
            }
        }
    }

    // 答案
    long long res = 0;
    for (int j = 0; j < 3; ++j) chmax(res, dp[N][j]);
    cout << res << endl;
}
```

　

# 5.2 (子集合加總問題)

可以用與第 5.4 節中說明的「背包問題」幾乎相同的方式解決。定義部分問題如下。

-----

`dp[i][j]` ← 布林值（true / false），用來表示從最初的 i 個整數中所選的一些數字的
總和是否等於 j

-----

可以用與背包問題幾乎相同的方式來解決。在背包問題中，以已知 `dp[i][0]`, `dp[i][1]`, ... 的值為前提，思考求出 `dp[i+1][0]`, `dp[i+1][1]`, ... 的值的轉移式。對於部分和問題也是一樣的。當思考 `dp[i+1][j]` 的值時，分為選擇 `a[i]` 和不選擇 `a[i]` 的狀況來考慮。如下述。

　

```cpp
// 不選擇 a[i] 的狀況
if (dp[i][j]) dp[i+1][j] = true;

// 選擇 a[i] 的狀況
if (j >= a[i] && dp[i][j-a[i]]) dp[i+1][j] = true;
```

　

綜上所述，可以實作如下。計算複雜度為 O(NW) 。



```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // 輸入
    int N, W;
    cin >> N >> W;
    vector<int> a(N);
    for (int i = 0; i < N; ++i) cin >> a[i];

    // DP
    vector<vector<bool>> dp(N+1, vector<bool>(W+1, false));
    dp[0][0] = true;
    for (int i = 0; i < N; ++i) {
        for (int j = 0; j <= W; ++j) {
            // 不選擇 a[i] 的狀況
            if (dp[i][j]) dp[i+1][j] = true;

            // 選擇 a[i] 的狀況
            if (j >= a[i] && dp[i][j-a[i]]) dp[i+1][j] = true;
        }
    }

    // 答案
    if (dp[N][W]) cout << "Yes" << endl;
    else cout << "No" << endl;
}
```

　

# 5.3 (TDPC A - Contest)

這是由部分和問題稍微延伸的問題。透過針對部分和問題的動態規畫法，可以作出一個陣列，用來表示是否能夠以O(NW)的計算複雜度分別合成1、2、...、W。藉此，求出在1、2、...、W中可合成的值有多少個。關於具體的演算法，記載於以下文章。

[AtCoder TDPC A - Contest](https://drken1215.hatenablog.com/entry/2020/12/21/153600)

　

# 5.4 (在 K 個以内合成 W )

考慮一個問題，從 N 個整數 a[0], a[1], ..., a[N-1] 中選擇 K 個以內整數，以使它們的總和為 W。乍看之下，似乎需要 O(NKW) 的計算複雜度。也就是說，可以考慮定義如下的陣列 `dp`

-----

`dp[i][j][k]` ← 布林值(true / false)，用來表示是否可以從前 i 個整數中選擇數字以使總和為j。其中選擇的個數為 k 個以內

-----

透過思考像這樣的陣列轉移，確實可以解決這個問題。例如也可以組成類似以下程式碼的循環。但是，這樣一來計算複雜度將為 O(NKW)。


　

```cpp
for (int i = 0; i < N; ++i) {
    for (int j = 0; j <= W; ++j) {
        for (int k = 0; k <= K; ++k) {
            // 不選擇 a[i] 的狀況
            if (dp[i][j][k])
                dp[i+1][j][k] = true;
               
            // 選擇 a[i] 的狀況
            if (j >= a[i] && k >= 1 && dp[i][j-a[i]][k-1])
                dp[i+1][j][k] = true;
        }
    }
}
```

　

但實際上，我們可以透過定義如下的陣列 `dp` 來解決這個問題。相對於先前的 `dp` 陣列是布林值，這個 `dp` 陣列包含更豐富的資訊。

-----

`dp[i][j]` ← 從前 i 個整數中選擇數個，以使總和為 j 的方法中，所選整數之個數的最小值

------

轉移本身可以與之前的背包問題或部分和問題，以幾乎相同的方式思考。


　

```cpp
// 不選擇 a[i] 的狀況
chmin(dp[i+1][j], dp[i][j]);

// 選擇 a[i] 的狀況 (+1 是增加1個的意思 )
if (j >= a[i]) chmin(dp[i+1][j], dp[i][j-a[i]] + 1);
```

　

最後，透過檢查 `dp[N][W]` 是否為 K 以下，可以判斷是否能實現目的。

　

```cpp
#include <iostream>
#include <vector>
using namespace std;
template<class T> void chmax(T& a, T b) { if (a < b) a = b; }
template<class T> void chmin(T& a, T b) { if (a > b) a = b; }

// 表示無限大的值
const int INF = 1<<29;

int main() {
    // 輸入
    int N, K, W;
    cin >> N >> K >> W;
    vector<int> a(N);
    for (int i = 0; i < N; ++i) cin >> a[i];

    // 陣列 dp
    vector<vector<int>> dp(N+1, vector<int>(W+1, INF));

    // 初始條件
    dp[0][0] = 0;

    // 迴圈
    for (int i = 0; i < N; ++i) {
        for (int j = 0; j <= W; ++j) {
            // 不選擇 a[i] 的狀況
            chmin(dp[i+1][j], dp[i][j]);

            // 選擇 a[i] 的狀況
            if (j >= a[i]) chmin(dp[i+1][j], dp[i][j-a[i]] + 1);
        }
    }

    // 若最小値為 K 以下，則Yes
    if (dp[N][W] <= K) cout << "Yes" << endl;
    else cout << "No" << endl;
}
```

　

# 5.5 (數量不限的子集合加總問題)

這個問題的難度比至今為止的問題更高。讓我們定義以下的陣列 dp 。

-----

`dp[i][j]` ← 只用前 i 個整數且允許有重複，是否可以使總和等於 j 

-----

一般的子集合加總問題中會進行如下轉移。

```cpp
dp[i+1][j] |= dp[i][j];
dp[i+1][j] |= dp[i][j-a[i]];
```

但在這個情況下，同一整數 `a[i]` 可以被使用多次。上述的轉移式中，`a[i]` 只能被使用一次。因此，將轉移改為以下方式：

```cpp
dp[i+1][j] |= dp[i][j];
dp[i+1][j] |= dp[i+1][j-a[i]];
```

透過這樣的修改，變成可以多次使用 `a[i]` 的狀態。計算複雜度仍然是 O(NW)。

　

```cpp
#include <iostream>
#include <vector>
using namespace std;
template<class T> void chmax(T& a, T b) { if (a < b) a = b; }
template<class T> void chmin(T& a, T b) { if (a > b) a = b; }

int main() {
    // 輸入
    int N, W;
    cin >> N >> W;
    vector<int> a(N);
    for (int i = 0; i < N; ++i) cin >> a[i];

    // DP 表
    vector<vector<bool>> dp(N+1, vector<bool>(W+1, false));

    // 初始條件
    dp[0][0] = true;

    // 迴圈
    for (int i = 0; i < N; ++i) {
        for (int j = 0; j <= W; ++j) {
            if (dp[i][j]) dp[i+1][j] = true;
            if (j >= a[i] && dp[i+1][j-a[i]]) dp[i+1][j] = true;
        }
    }

    // 答案
    if (dp[N][W]) cout << "Yes" << endl;
    else cout << "No" << endl;
}
```

　

# 5.6 (數量有限的子集合加總問題)

這個問題比5.5（數量不限的子集合加總問題）更加困難。在5.5中，可以無限制的使用各整數，但這題制定了使用每個整數  `a[i]` 的次數上限 `m[i]` 。因此，將陣列 dp 陣列的定義修改如下。

------

`dp[i][j]` ← 只用前 i 個整數來合成 j 的方法中，使用最後一個整數之次數的**最小值**

------

此時，可以將從 `dp[i]` 到 `dp[i+1]` 的轉移精簡如下。另外，無法合成j的情況下，令`dp[i][j] = INF` （`INF` 代表無限大的值） 。


　

#### 不使用 `a[i]` 的狀況

如果 `dp[i][j] < INF` ， 由於不使用 `a[i]` 即可合成 `j` ，因此 

`chmin(dp[i+1][j], 0)`

　

#### `dp[i+1][j-a[i]] < m[i]`  的狀況

由於可以使用進一步追加的 `a[i]` ，因此

`chmin(dp[i+1][j], dp[i+1][j-a[i]] + 1)`

　

如下述程式碼所示實作以上處理。計算複雜度為 O(NW) 。

　

```cpp
#include <iostream>
#include <vector>
using namespace std;
template<class T> void chmax(T& a, T b) { if (a < b) a = b; }
template<class T> void chmin(T& a, T b) { if (a > b) a = b; }

// 表示無限大的值
const int INF = 1<<29;

int main() {
    // 輸入
    int N, W;
    cin >> N >> W;
    vector<int> a(N), m(N);
    for (int i = 0; i < N; ++i) cin >> a[i] >> m[i];

    // DP
    vector<vector<int>> dp(N+1, vector<int>(W+1, INF));

    // 初始條件
    dp[0][0] = 0;

    // 迴圈
    for (int i = 0; i < N; ++i) {
        for (int j = 0; j <= W; ++j) {
            // 用 i 個即可合成 j 的狀況
            if (dp[i][j] < INF) chmin(dp[i+1][j], 0);

            // 用 i+1 個且不到 m[i] 個即可合成 j-a[i] 的話，亦可合成 j 
            if (j >= a[i] && dp[i+1][j-a[i]] < m[i]) {
                chmin(dp[i+1][j], dp[i+1][j-a[i]]+1);
            }
        }
    }

    // 答案
    if (dp[N][W] < INF) cout << "Yes" << endl;
    else cout << "No" << endl;
}
```

　

# 5.7 (最長共同子序列問題)

本問題是第5.5節解說的「求出編輯距離的問題」的類似題。與求出編輯距離的動態規畫法一樣，定義如下陣列 `dp`。另外，最長共同子序列通常稱為 LCS（Longest Common Subsequence）。

-----

`dp[i][j]` ← 字串 S 的前 i 個字元和字串 T 的前 j 個字元之間的LCS長度

-----

然後，根據「S 的前 i 個字元」及「T 的前 j 個字元」各自的最後一個字元是如何對應來區分狀況。


　

#### 當 S 的第 i 個字元對應到 T 的第 j 個字元時 

- 若為 `S[i-1] == T[j-1]` ，則 `chmax(dp[i][j], dp[i-1][j-1]+1)`
- 否則， `chmax(dp[i][j], dp[i-1][j-1])`

　

#### 添加 S 的第 i 個字元

針對「S 的前 i - 1 個字元」與「T 的前 j 個字元」的對應，添加 S 的第 i 個字元。此時，LCS的長度不會改變，故如下所示。

`chmax(dp[i][j], dp[i-1][j])`

　

#### 添加 T 的第 j 個字符

針對「S的前 i 個字元」與「T的前 j - 1 個字元」的對應，添加 T 的第 j 個字元。此時，LCS的長度不會改變，故如下所示。

`chmax(dp[i][j], dp[i][j-1])`

　

綜上所述，可以如下進行實作。計算複雜度為 O(NM)（N：S的長度，M：T的長度）。


　

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;
template<class T> void chmax(T& a, T b) { if (a < b) a = b; }
template<class T> void chmin(T& a, T b) { if (a > b) a = b; }

int main() {
    // 輸入
    string S, T;
    cin >> S >> T;
    int N = S.size(), M = T.size();

    // DP 表
    vector<vector<int>> dp(N+1, vector<int>(M+1, 0));

    // 迴圈
    for (int i = 0; i <= N; ++i) {
        for (int j = 0; j <= M; ++j) {
            // 當 S 的第 i 個字元與 T 的第 j 個字元相等時
            if (i > 0 && j > 0) {
                if (S[i-1] == T[j-1]) chmax(dp[i][j], dp[i-1][j-1] + 1);
                else chmax(dp[i][j], dp[i-1][j-1]);
            }
          
            // 添加 1 字元至 S 
            if (i > 0) {
                chmax(dp[i][j], dp[i-1][j]);
            }
          
            // 添加 1 字元至 T 
            if (j > 0) {
                chmax(dp[i][j], dp[i][j-1]);
            }
        }
    }

    // LCS
    cout << dp[N][M] << endl;
}
```

　　

# 5.8 (RUPC 2018 day1-D 水槽)


本問題是第5.6節中解說的「區間分割最適化問題」的一個類似題。關於這個問題的解決方案，在以下文章中進行詳細解說。

[AOJ 2877 水槽 (RUPC 2018 day1-D)](https://drken1215.hatenablog.com/entry/2020/12/21/202300)

　

# 5.9 (最佳二元搜尋樹問題)

本問題是被稱為最佳二元搜尋樹問題的著名問題。結論來說，可以按照以下方式定義DP表。

------

`dp[i][j]` ←  在只考慮 `a[i:j]` 時，使史萊姆合體到 1 隻為止前所需的最小操作成本， `a[i:j]` 是用來表示史萊姆大小的陣列 `a` 的子陣列 `a[i:j]` (`a[i], a[i+1], …, a[j-1]`）

------

此時，如下思考DP的轉移。首先，考慮子陣列 `a[i:j]` 中的史萊姆變成1隻之前的狀態。之前的狀態中，應該為史萊姆有2隻的狀態。假設這2個史萊姆分別來自子陣列`a[i:k]` 、 `a[k:j]` 。這時，史萊姆的合體成本變為 `dp[i][k] + dp[k][j] + s[i:j] `（將`s[i:j]` 定義為`a[i], a[i+1], …, a[j-1]` 的總和）。

對滿足 i < k < j 的每個 k，進行以上的狀況區分。因此，根據動態規畫法的更新部分可以表示如下（注意實際上 `S[i:j]` 這種表示在 C++ 中是不存在的）。


```cpp
for (int k = i+1; k < j; ++k) {
    chmin(dp[i][j], dp[i][k] + dp[k][j] + s[i:j]);
}
```


最後，考慮關於動態規畫法的更新順序。在上述的轉移式中，以已知 `dp[i][k]`和 `dp[k][j]` 的值為前提，將 `dp[i][j]` 的值進行更新。在此，k - i 和 j - k 都比 j - i 小。因此，更新陣列 `dp[i][j]` 時，只需要按照 j - i 的大小順序進行更新即可。

根據以上說明，可以實作如下程式碼。計算複雜度為 O(N^3)。又，令陣列 `a` 的累積和為 `S` 。這用在上述的轉移式中 `s[i:j]` 的計算。具體來說，可計算`s[i:j] = S[j] - S[i]`。如果想要詳細了解累積和，請參閱以下文章。

[無需思考就能寫出累積和！](https://qiita.com/drken/items/56a6b68edef8fc605821)


　

```cpp
#include <iostream>
#include <vector>
using namespace std;

template<class T> inline bool chmin(T& a, T b) {
    if (a > b) {
        a = b;
        return 1;
    }
    return 0;
}

// 表示無限大的值
const long long INF = 1LL<<60;

int main() {
    // 輸入
    int N;
    cin >> N;
    vector<long long> a(N);
    for (int i = 0; i < N; ++i) cin >> a[i];

    // 取累積和
    vector<long long> S(N+1, 0);
    for (int i = 0; i < N; ++i) S[i+1] = S[i] + a[i];

    // DP
    vector<vector<long long>> dp(N+1, vector<long long>(N+1, INF));

    // 初始條件
    for (int i = 0; i < N; ++i) dp[i][i+1] = 0;

    // 更新
    for (int bet = 2; bet <= N; ++bet) {
        for (int i = 0; i + bet <= N; ++i) {
            int j = i + bet;

            // 更新 dp[i][j]
            for (int k = i+1; k < j; ++k) {
                chmin(dp[i][j], dp[i][k] + dp[k][j] + S[j]-S[i]);
            }
        }
    }

    cout << dp[0][N] << endl;
}
```
