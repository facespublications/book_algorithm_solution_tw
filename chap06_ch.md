# 6.1 (座標壓縮)

在競技程式設計中，有被稱為「座標壓縮」的處理。座標壓縮通常被要求作為較難問題的前處理步驟。

首先，對整數數列 `a[0]`, `a[1]`, ..., `a[N-1]` 進行升冪排列（排序為 O(N log N)，請參考第 12 章）。令排序後的陣列為 `b[0]`, `b[1]`, ..., `b[N-1]`。此時，對於每個 `i`，最好找到一個索引 `j`，使得 `b[j] = a[i]`。

這可以使用第 6.2 節中解說的 std::lower_bound 函數並如下求出。首先，藉由 `lower_bound(b.begin(), b.end(), a[i])` ，求出陣列 `b` 上的迭代器。然後，算出該迭代器與表示陣列 `b` 開頭的迭代器 `b.begin()` 之間的差，即可得到 `b[j] = a[i]` 的索引 `j`。


　

```cpp
int j = lower_bound(b.begin(), b.end(), a[i]) - b.begin();
```

　

對每個元素 `a[0]`, `a[1]`, ..., `a[N-1]` 進行上述處理，則整體的計算複雜度將為 O(N log N)。具體來說，可以按照以下方式實作。對整個陣列 `b` 進行排序，可以使用 `sort(b.begin(), b.end())` 來實現。

　

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    // 輸入
    int N;
    cin >> N;
    vector<int> a(N);
    for (int i = 0; i < N; ++i) cin >> a[i];

    // 製作a的複製陣列並排序
    auto b = a;
    sort(b.begin(), b.end());

    // 對於各個 a 在 b 之中是第幾個
    vector<int> res(N);
    for (int i = 0; i < N; ++i) {
        res[i] = lower_bound(b.begin(), b.end(), a[i]) - b.begin();
    }

    // 輸出
    for (int i = 0; i < N; ++i) cout << res[i] << endl;
}
```

　

# 6.2 (ABC 077 C - Snuke Festival)

首先，將陣列 `a`、`b`、`c` 分別進行排序。然後對陣列 `b` 的每個元素 `b[j]`，分別求出滿足 `a[i] < b[j]` 的 `i` 的個數和滿足 `b[j] < c[k]` 的 `k` 的個數（在此使用 lower_bound ）。有關更具體的解法，記於以下文章。

[AtCoder ABC 077 C - Snuke Festival](https://drken1215.hatenablog.com/entry/2021/02/25/223800)

　

# 6.3 (第 7 回 JOI 正式選拔 題3 - 飛鏢)

這是一個非常高難度的問題，但可以透過「對半全列舉」和「二元搜尋」的思考方法來解決。有關這個問題的解法，記於以下文章。

[JOI 正式選拔 2018 C - 飛鏢](https://drken1215.hatenablog.com/entry/2020/12/04/035454)

　

# 6.4 (POJ No. 2456 Aggressive cows)

當解決最佳化問題時，思考「是否能考慮判斷問題並進行二元搜索」的方法通常是有效的（參照第 6.7 節）。在這個問題中，考慮以下的判斷問題吧。

-----

從 N 間小屋中選擇 M 間，此時，是否可以使得任意兩間小屋的間隔都為 x 以上？

-----


原始問題的答案會是「使上述判斷問題答案為 Yes 的 x 的最大值」。因此，如果上述判斷問題可以解決，就可以藉由二元搜尋法求出答案。具體來說，首先，準備變數 `left` 和 `right` 如下。

- 令 `left` 的初始值為 0（答案確定為 Yes）
- 令 `right` 的初始值為 INF（設定一個答案確定為 No 的值 INF）

然後，令 `x = (left + right) / 2`，針對 x 解決上述判斷問題。接著，如下述進行即可。

- 如果判斷問題的答案為 Yes，則使 `left ← x`
- 如果判斷問題的答案為 No，則使 `right ← x`

最後，考慮判斷問題的解法。判斷問題是從左開始按照順序檢查小屋，如果「與最後選擇的小屋距離 x 以上的話，則選擇該小屋」這樣進行即可（第 7 章所學的貪婪法的一種）。有關具體處理內容，請參考以下程式碼。由於判斷問題的可用 O(N)的計算複雜度來解，因此整體的計算複雜度為 O(N log A)（其中 A 是小屋之間距離的最大值）。

另外在下面的實作中，為了簡化，將變數 `right` 的初始值設置為表示「足夠大的值」的 `INF`，因此正確的計算複雜度為 O(N log INF)。


　

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// 足夠大的值
const long long INF = 1LL<<50;

int main() {
    // 輸入
    int N, M;
    cin >> N >> M;
    vector<long long> a(N);
    for (int i = 0; i < N; ++i) cin >> a[i];
    sort(a.begin(), a.end());

    // 解開「是否能使所有的間隔皆為 x 以上來選出 M 個小屋？」這個判斷問題
    // 求出答案為 Yes 的 x 的最大值
    long long left = 0, right = INF;
    while (right - left > 1) {
        long long x = (left + right) / 2;

        int cnt = 1; // 能夠選幾個小屋 (選取最前面的小屋)
        int prev = 0; // 前一次小屋的 index
        for (int i = 0; i < N; ++i) {
            // 如果空出 x 以上的間隔的話，就採用
            if (a[i] - a[prev] >= x) {
                ++cnt;
                prev = i;
            }
        }

        // 判斷問題為 Yes 或 No 
        if (cnt >= M) left = x;
        else right = x;
    }
    cout << left << endl;
}
```

　

# 6.5 (ARC 037 C - 億格計算)

在「求第 K 小的值」或「求中位數」這樣設定的問題中，通常二元搜尋法會有效。有關具體的演算法，記於以下文章。

[AtCoder ARC 037 C - 億格計算](https://drken1215.hatenablog.com/entry/2021/07/03/041500)

　

# 6.6 (ABC 026 D - 高橋同學 1 號球)

這是一個求解方程式 f(t) = 0 的類型的問題。對這樣的問題，二元搜索法可能會有效（有時也稱為二元法）。有關具體的演算法，記於以下文章。

[AtCoder ABC 026 D - 高橋同學 1 號球](https://drken1215.hatenablog.com/entry/2021/07/03/173400)

　

# 6.7 (ARC 101 D - Median of Medians)

在「求第 K 小的值」或「求中位數」這樣設定的問題中，通常二元搜尋法會有效。這是一個相當困難的問題。有關具體的演算法，記於以下文章。

[AtCoder ABC 107 D - Median of Medians](https://drken1215.hatenablog.com/entry/2018/09/08/011500?_ga=2.135966193.1107495987.1625194483-791597362.1625194483)

