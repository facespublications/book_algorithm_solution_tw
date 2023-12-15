# 15.1 (基本切割集的性質)

對於無向圖 G 的最小生成樹 T 和其所包含的邊 e，將關於 T , e 的基本切割集設為 C。將除了 C 所包含的 e 以外的任意邊設為 f 。此時，T' = T - e + f 也形成生成樹。由於 T 是最小生成樹，形成如下式。

　　　w(T) <= w(T') = w(T) - w(e) + w(f)

因此，可以導出 w(e) <= w(f) 的結論。

　

# 15.2 (中位數為最小的生成樹)

設連通的無向圖 G（頂點數 N、邊數 M）的最小生成樹為 T，其他的任意生成樹為 S。將 T 中包含的邊按權重從小到大排序成 t[1]、t[2]、...、t[N-1]，將 S 中包含的邊按權重從小到大排序成 s[1]、s[2]、...、s[N-1]。

此時，可知下列式子成立。

- t[1] <= s[1]
- t[2] <= s[2]
- ...
- t[N-1] <= s[N-1]

也就是說，最小生成樹不僅滿足（t 的總和） <=（s 的總和），而且對「該生成樹中包含的每條邊的權重中，第 k 小的值」而言也是最小生成樹。

由此可知，將最小生成樹（通常有複數種可能）中包含的邊的權重，按照從小到大排序所得到的樹列，一直是一致的。

有關以上性質的證明，在[問題的官方解説](https://jag-icpc.org/?plugin=attach&refer=2012%2FPractice%2F%E6%A8%A1%E6%93%AC%E5%9C%B0%E5%8C%BA%E4%BA%88%E9%81%B8%2F%E8%AC%9B%E8%A9%95&openfile=MedianTree.pdf) 中有詳細記載，可以作為參考。

此外，作為解決問題的具體演算法，應用一般求最小生成樹的克魯斯卡法即可。時間複雜度為 O(N log M)。


　

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// Union-Find
struct UnionFind {
    vector<int> par;

    UnionFind() { }
    UnionFind(int n) : par(n, -1) { }
    void init(int n) { par.assign(n, -1); }
    
    int root(int x) {
        if (par[x] < 0) return x;
        else return par[x] = root(par[x]);
    }
    
    bool issame(int x, int y) {
        return root(x) == root(y);
    }
    
    bool merge(int x, int y) {
        x = root(x); y = root(y);
        if (x == y) return false;
        if (par[x] > par[y]) swap(x, y); // merge technique
        par[x] += par[y];
        par[y] = x;
        return true;
    }
    
    int size(int x) {
        return -par[root(x)];
    }
};

int main() {
    // 邊數
    int N, M;
    while (cin >> N >> M, N) {
        // 表示邊的結構
        using pint = pair<int, int>;  // 兩端點
        using Edge = pair<long long, pint>;  // (權重, 兩端點)
        
        // 輸入
        vector<Edge> edges(M);
        for (int i = 0; i < M; ++i) {
            cin >> edges[i].second.first >> edges[i].second.second
                >> edges[i].first;
            --edges[i].second.first;
            --edges[i].second.second;
        }

        // 將邊按照權重排序
        sort(edges.begin(), edges.end());

        // Kruskal 法 
        UnionFind uf(N);
        int num = 0;  // 邊的條數
        long long res = 0;  // 答案
        for (auto e: edges) {
            long long w = e.first;
            int u = e.second.first, v = e.second.second;

            // 無法添加邊 (u, v) 的情況
            if (uf.issame(u, v)) continue;

            // 添加邊 (u, v) 時
            uf.merge(u, v);
            ++num;

            // 到達中位數
            if (num == (N + 1) / 2) {
                res = w;
                break;
            }
        }
        cout << res << endl;
    }
}
```

　

# 15.3 (必須包含在最小生成樹中的邊)

首先，考慮直接的解法吧。對圖 G 的各邊 e，比較下述的值。

+ 原始圖 G 的最小生成樹權重
+ 從原始圖中刪除邊 e 後形成的圖 G - e 的最小生成樹權重

如果這兩者不相等，那麼這條邊 e 就是絕對必要的。

因此，對所有邊 e 進行上述處理，就可以解開問題。

由於最小生成樹計算一次需要 O(M log N) 的計算複雜度，所以整體的計算複雜度為 O(M^2 log N)。然而，這樣的計算複雜度非常龐大。

　

因此，首先在原始圖上求出最小生成樹 T。

此時，「絕對需要」的邊應該至少會被包含在 T 中。因此，不像先前對 G 所含的所有邊 e 進行檢查，而只考慮 T 所含的邊即可。藉此，可以大幅減少需要檢查的邊的數量。

實際上，由於 T 是樹，需要檢查的邊的數量為 N-1 條。因此，計算複雜度將改善為 O(MN log N)。

　

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// Union-Find
struct UnionFind {
    vector<int> par;

    UnionFind() { }
    UnionFind(int n) : par(n, -1) { }
    void init(int n) { par.assign(n, -1); }
    
    int root(int x) {
        if (par[x] < 0) return x;
        else return par[x] = root(par[x]);
    }
    
    bool issame(int x, int y) {
        return root(x) == root(y);
    }
    
    bool merge(int x, int y) {
        x = root(x); y = root(y);
        if (x == y) return false;
        if (par[x] > par[y]) swap(x, y); // merge technique
        par[x] += par[y];
        par[y] = x;
        return true;
    }
    
    int size(int x) {
        return -par[root(x)];
    }
};

// 表示邊的結構
using pint = pair<int, int>;  // 兩端點
using Edge = pair<long long, pint>;  // (權重, 兩端點)

int main() {
    // 輸入
    int N, M;
    cin >> N >> M;
    vector<Edge> edges(M);
    for (int i = 0; i < M; ++i) {
        int u, v, w;
        cin >> u >> v >> w;
        --u, --v;
        edges[i] = Edge(w, pint(u, v));
    }

    // 將各邊依權重從小到大的順序排序
    sort(edges.begin(), edges.end());

    // 求出刪除第 id 邊時的最小生成樹的函數
    // id == -1 時，求出一般的最小生成樹
    // res 儲存有構成最小生成樹的邊的編號
    auto calc = [&](vector<int> &res, int id = -1) -> long long {
        long long cost = 0;
        res.clear();

        // Kruskal 法
        UnionFind uf(N);
        for (int i = 0; i < edges.size(); ++i) {
            if (i == id) continue;
            int u = edges[i].second.first, v = edges[i].second.second;
            if (uf.issame(u, v)) continue;
            res.push_back(i);
            cost += edges[i].first;
            uf.merge(u, v);
        }

        // 不滿足 N-1 條邊時，為不連通
        if (res.size() < N - 1) return (1LL<<60);
        return cost;
    };
    
    // 求原始圖的最小生成樹
    vector<int> mst;
    long long base = calc(mst);

    // 將最小生成樹 mst 所含的各邊 e 一條一條除去並檢查
    vector<int> dammy;
    long long num = 0, res = 0;
    for (auto id : mst) {
        if (calc(dammy, id) > base) {
            ++num;
            res += edges[id].first;
        }
    }
    cout << num << " " << res << endl;
}
```

　

