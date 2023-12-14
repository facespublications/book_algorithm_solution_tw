# 4.1

與程式碼4.5相同，可以實作如下。在程式碼4.5中的基本情況，有 N = 0, 1 的 2 種值，但在下面的實作中，基本情況有 N = 0, 1, 2 的 3 種值。

　

```cpp
#include <iostream>
using namespace std;

int func(int N) {
    // 基本情況
    if (N == 0) return 0;
    else if (N == 1) return 0;
    else if (N == 2) return 1;

    // 遞迴呼叫
    return func(N - 1) + func(N - 2) + func(N - 3);
}

int main() {
    int N;
    cin >> N;
    cout << func(N) << endl;
}
```



　

# 4.2

在問題 4.1 中所示的程式碼具有指數級時間的計算複雜度。透過4.4 節中解說的記錄化，來進行效率化。可以實作如下。計算複雜度將變成O(N)。

　

```cpp
#include <iostream>
#include <vector>
using namespace std;

// 將fibo(N) 的答案記錄化的陣列
vector<long long> memo;

long long func(int N) {
    // 基本情況
    if (N == 0) return 0;
    else if (N == 1) return 0;
    else if (N == 2) return 1;

    // 檢查記錄（如果已經計算完畢，就回傳答案）
    if (memo[N] != -1) return memo[N];

    // 將答案記錄化，同時進行遞迴呼叫
    return memo[N] = func(N - 1) + func(N - 2) + func(N - 3);
}

int main() {
    int N;
    cin >> N;

    // 將記錄化用的陣列初始化為-1
    memo.assign(N + 1, -1);

    // 呼叫 fibo(N)
    cout << func(N) << endl;
}
```



　

# 4.3

二次方程式 x^2 = x + 1 的解為 α = (1 + √5) / 2、β = (1 - √5) / 2。此時，費波那契數列（一般項表示為 F[n]）的遞迴式可以變形為：

- F[n] - α F[n - 1] = β (F[n - 1] - α F[n - 2])
- F[n] - β F[n - 1] = α (F[n - 1] - β F[n - 2])

（此處不懂的讀者，請複習數學IIB 的三項遞迴式的求解方法）。特別是第一個式子表示了F[n] - α F[n - 1] 為公比 β 的等比數列。藉此，可以寫成如下式。

F[n] - α F[n - 1] = β^(n - 1)

同樣的，後半的式子可以寫成如下式。

F[n] - β F[n - 1] = α^(n - 1)

藉此，將 F[n - 1] 刪去的話，會變成下式。

F[n] = (α^n - β^n) / (α - β) = 1/√5 (α^n - β^n)

由上得證。

　

# 4.4

在程式碼4.5中，如果將計算 fibo(N) 所需的時間表示為 T(N)，則 T(N) = T(N-1) + T(N-2) + O(1) 成立（其中 T(0) 、 T(1) 為常數）。因此，T(N) 的增長方式與費波那契數列相同。另一方面，費波那契數列的第 N 項是以下的線性組合。

- ((1 + √5) / 2)^N
- ((1 - √5) / 2)^N

其中後者在 N → ∞ 時趨近於 0。因此，可以估計為 T(N) = O((1 + √5) / 2)^N)。

　

# 4.5 (ABC 114 C - 755)

藉由使用遞迴函數，可以像下圖一樣列舉出 N 以下的753數。

<img src=https://github.com/drken1215/book_algorithm_solution/blob/master/fig/sol4-5.png width=450mm>

此外，為了判斷「7 、 5 和 3 都至少出現一次」這個條件，可以設置用來管理以下問題的旗標變數。

- 各個位數中是否存在數字 7
- 各個位數中是否存在數字 5
- 各個位數中是否存在數字 3

活用第3.5 節所解說的「整數的二進制表示和位元運算」可以有效的處理這 3 個旗標。例如，可以用如下程式碼實作。

　

```cpp
#include <iostream>
using namespace std;

// N: 輸入
// cur: 現在的值
// use: 表示使用了7、 5 、 3 之中的哪一個 
// counter: 答案
void func(long long N, long long cur, int use, long long &counter){
    if (cur > N) return; // 基本情況
    if (use == 0b111) ++counter; // 將答案增加

    // 加上 7
    func(N, cur * 10 + 7, use | 0b001, counter);

    // 加上 5
    func(N, cur * 10 + 5, use | 0b010, counter);

    // 加上 3
    func(N, cur * 10 + 3, use | 0b100, counter);
}

int main() {
    long long N;
    cin >> N;
    long long counter = 0;
    func(N, 0, 0, counter);
    cout << counter << endl;
}
```



　

# 4.6

在程式碼 4.9 的遞迴函數

 `bool func(int i, int w, const vector<int> &a)`

中，如下所示準備一個記憶化用陣列，以儲存對引數 i 、 w的答案。

-----

`memo[i][w]`  ← `func(i, w, a)` 的答案

-----

使用這個方式，可以如下述程式碼進行實作。計算複雜度為 O(NW)。

　

```cpp
#include <iostream>
#include <vector>
using namespace std;

// 將func(i, w, a) 的答案記憶化的陣列
vector<vector<int>> memo;

// 0：false、1: true
int func(int i, int w, const vector<int> &a) {
    // 基本情況
    if (i == 0) {
        if (w == 0) return true;
        else return false;
    }

    // 檢查記憶（如果已經計算完畢，就回傳答案）
    if (memo[i][w] != -1) return memo[i][w];

    // 不選擇 a[i - 1] 時
    if (func(i - 1, w, a)) return memo[i][w] = 1;

    // 選擇 a[i - 1] 時
    if (func(i - 1, w - a[i - 1], a)) return memo[i][w] = 1;

    // 皆為 false 時則回傳 false
    return memo[i][w] = 0;
}

int main() {
    // 輸入
    int N, W;
    cin >> N >> W;
    vector<int> a(N);
    for (int i = 0; i < N; ++i) cin >> a[i];

    // 遞迴求解
    memo.assign(N+1, vector<int>(W+1, -1));
    if (func(N, W, a)) cout << "Yes" << endl;
    else cout << "No" << endl;
}
```

