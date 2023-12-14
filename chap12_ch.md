# 12.1

可以考慮各種方法。在此思考在排序後使用二元搜尋 (lower_bound()) 的方法吧。

首先，將陣列 `a` 排序並將產生的陣列設為 `b`（以不在陣列 `a` 中增加變更的方式進行）。此時，對 `a` 的每個元素 `a[i]`取得下述的值。

　　`lower_bound(b.begin(), b.end(), a[i]) - b.begin()`

藉此，可以求出`a[i]` 在 `b` 中為第幾個元素（由 0 開始）。計算複雜度分成如下部分：

- 排序部分：O(N log N)​
- 對各元素進行二元搜尋部分：O(N log N)​

因此，整體的計算複雜度為 O(N log N)。

　

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    // 輸入
    int N;
    cin >> N;
    vector<int> a(N);
    for (int i = 0; i < N; ++i) cin >> a[i];

    // 排序
    auto b = a;
    sort(b.begin(), b.end());

    // 對各元素進行二元搜尋
    for (int i = 0; i < N; ++i) {
        cout << lower_bound(b.begin(), b.end(), a[i]) - b.begin() << endl;
    }
}
```

　

# 12.2

考慮如何以最佳順序逛 N 家店。基本上，從價格最低的店（`A[i]` 較小）依序開始即可。

首先，依價格便宜順序對 N 家店進行排列。這個處理可由 O(N log N) 的計算複雜度算出。然後按照這個排列的順序，開始遊覽商店直到購買數達到 M 為止。有關具體的演算法，記於以下文章。

[AtCoder ABC 121 C - Energy Drink Collector](https://drken1215.hatenablog.com/entry/2019/05/15/012700)

　

# 12.3

利用堆積進行。準備如下2種堆積吧。

- 堆積 `low` ：到目前為止插入的整數中，管理最小的 K 個值。
- 堆積 `high` ：到目前為止插入的整數中，管理除了前 K 個以外的值。

其中，使堆積 `low` 可以進行「取得最大値」、「刪除最大値」；使堆積 `high` 可以進行「取得最小値」、「刪除最小値」。

首先，將 `a[0]`, `a[1]`, ..., `a[K-1]` 插入堆積 `low` 。然後，對 i = K, K+1, ..., N-1，進行以下處理：

-----

將堆積 `low` 的最大值設為 M。

- 如果 `a[i] >= M`，將 `a[i]` 插入堆積 `high`。
- 如果 `a[i] < M`，則
  - 移除堆積 `low` 的最大值，並將其插入堆積 `high`。
  - 在堆積 `low` 中插入值 `a[i]`。

完成這些操作後，堆積 `low` 的最大值即為答案。

-----

　

# 12.4

前半部分與「合併排序的計算複雜度分析」完全相同。關於後半部分，T(N) = O(N (log N)^2)。

　

# 12.5 (median of medians)

對於在 N 個元素中找到第 K 小的值，乍看之下會認為不可能有比「排序並輸出第 K 個」這樣以 O(N log N) 計算複雜度更快的解法。但已知有一種根據分治法的 O(N) 演算法（雖然由於常數倍數的影響很大，實際上未必一定很快）。

現在，將找到陣列 `a` 中第 k 小的值的演算法設為 rec(a, k)，如下思考遞迴演算法吧。其中一部分的處理內容用中文說明。

　

```cpp
int rec(a, k) {
    // 終止條件
    if (a.size() <= 100) {
         sort(a.begin(), a.end());
         return a[k];
    }
  
    // 將 a 的元素每 5 個一組切割，然後收集每個組的中位數。
    vector<int> a2;
    for (int i = 0; i < a.size(); i += 5) {
        a2.push_back(a[i:i+5] のメディアン); 
    }
  
    // 求 a2 的中位數
    int m = rec(a2, a.size()/10);
  
    // 將 a 分為 3 組
    vector<int> p, q, r;
    for (int i = 0; i < a.size(); ++i) {
        if (a[i] < m) p.push_back(a[i]);
        else if (a[i] == m) q.push_back(a[i]);
        else r.push_back(a[i]);
    }

    // 遞迴地求解
    int res;
    if (k <= p.size()) return rec(p, k);
    else if (k <= p.size() + q.size()) return m;
    else return rec(r, k - p.size() - q.size());
}
```

　

首先，終止條件是當陣列的大小為 100（其他常數亦可）以下時，對陣列進行排序處理。由於此時保證陣列大小為常數以下，因此這個處理可以視為常數時間。現在，假設陣列 a 的大小為 N，將此演算法的運算時間表示為 T(n)，則可以使用以下的遞迴式進行評估。

　　T(n) <= T(n/5) + T(7n/10) + O(n)

首先，T(n/5) 表示求出陣列 `a2` 的中位數的處理。接著，T(3n/4) 表示遞迴求解的部分。值得注意的是下兩式會成立。

- `p.size()` <= 7n/10
- `r.size()` <= 7n/10

例如，考慮 `p` 時，在將陣列 `a` 每 5 個分開的群組中，就中位數大於 m 的群組（其群組數量大約有 n/10 個）而言，其前 3 個元素確定為 m 以上。換句話說，至少有 3n/10 個元素確定為 m 以上。由上可知陣列 `p` 的大小是 7n/10以下。對於陣列 `r`也是相同的情況。

在 T(n) 的遞迴式中，n/5 + 7n/10 = 9n/10，比 n 還小。也就是說，在根據分治法進行遞迴時，可以朝大小的總和比原始問題小的部分問題群組歸攏。像這種時候，與第 12.4.3 節的討論（當 a < b 時）同樣，可以證明計算複雜度為 O(n)。

如上所述，將根據分治法的遞迴演算法，即分割過的部分問題的大小總和比原始問題小的演算法特別稱為**縮小法**。

　

# 12.6 (Tenka1 Programming Contest F - ModularPowerEquations!!)

這是一個極困難的問題。可以設計一種根據分治法的遞迴演算法。形成一種結構，在計算複雜度的分析中，可以應用**縮小法**，使計算複雜度能夠被抑制降低。有關解法的詳細內容，請參閱官方解說。

[官方解說](https://img.atcoder.jp/tenka1-2017/editorial.pdf)

　

















