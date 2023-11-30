# 18.1 (樹木的最大穩定集合)

如第 17 章所看到的，最大穩定集合問題通常是 NP 困難，很難解決。但是對於特殊的圖，有可能有效地找到最大穩定集合。作為代表例，是在二部圖上求最大穩定集合的問題，在問題 16.5 中也看過。

本次考慮求出樹的最大穩定集合的問題。實際上，我們可以通過貪婪地選擇樹的葉頂點來解決這個問題。

選擇樹的一個葉頂點v，證明「當存在不包含頂點 v 的穩定集合  S 時，可以在不減少其大小的情況下，轉換成包含頂點 v 的穩定集合」。將與葉頂點 v 相鄰的節點設為 u，結果如下。

- 如果 S 不包含 u，則在 S 中添加 v 後仍為穩定集合
- 如果 S 包含 u，則從 S 中刪除 u 並添加 v 來取代後，仍為穩定集合

無論哪種情況，都可以在不減少穩定集合大小的情況下，轉換成包含葉頂點 v 的穩定集合。

據上所述，藉由貪婪地選擇樹的葉頂點，並將這些葉頂點和相鄰頂點刪去的方法，可以找到最大穩定集合。

在實作上，如第 13.10 節的「樹上的動態規劃法」所解說的內容，選擇 1 個樹的頂點作為根的方法很方便。可以如以下程式碼來實作。設頂點數為 N ，則計算複雜度為 O(N)。

　

```cpp
#include <iostream>
#include <vector>
using namespace std;
using Graph = vector<vector<int>>;

vector<bool> used;
void rec(const Graph &G, int v, int p = -1) {
    // 子頂點之中是否有已選取的頂點
    bool exist = false;
    for (auto ch: G[v]) {
        if (ch == p) continue;

        // 遞迴搜尋
        rec(G, ch, v);
        if (used[ch]) exist = true;
    }

    // 如果子頂點之中沒有已選取的頂點，則將其選取
    if (!exist) used[v] = true;
}

int main() {
    // 輸入
    int N;
    cin >> N;
    Graph G(N);
    for (int i = 0; i < N - 1; ++i) {
        int u, v;
        cin >> u >> v;
        --u, --v;
        G[u].push_back(v);
        G[v].push_back(u);
    }

    // 將頂點 0 作為根進行搜尋
    used.assign(N, false);
    rec(G, 0);

    // 計數
    int res = 0;
    for (int v = 0; v < N; ++v) {
        if (used[v]) ++res;
    }
    cout << res << endl;
}
```

　

# 18.2 (EDPC P - Independent Set)

這是一個將樹上的穩定集合進行計數的問題。通常為 NP 困難問題（特別是 #P 完全問題），但透過運用樹的特殊結構的解法，可以在多項式時間內求解。具體來說，可以使用根據動態規劃法的演算法，即在第 18.2 節中解說的「針對樹上的加權最大穩定集合問題的動態規劃法」。將陣列 `dp1` 和 `dp2` 定義如下。

-----

- `dp1[v]` ← 在以頂點 v 為根的子樹中，不包含頂點 v 的穩定集合的數量
- `dp2[v]` ← 在以頂點 v 為根的子樹中，包含頂點 v 的穩定集合的數量

-----

計算複雜度為 O(N)。



```cpp
#include <iostream>
#include <vector>
using namespace std;
using Graph = vector<vector<int>>;

// MOD
const int MOD = 1000000007;

// 樹上的動態規劃法
// dp1: 不選擇子樹的根的狀況，dp2: 選擇子樹的根的狀況
vector<long long> dp1, dp2;
void rec(const Graph &G, int v, int p = -1) {
    // 遞迴地搜尋子頂點
    for (auto ch: G[v]) {
        if (ch == p) continue;
        rec(G, ch, v);
    }

    // dp1: 各子樹的穩定集合（不受根的選擇影響）的數量的乘積
    // dp2: 各子樹的穩定集合（不選擇根）的數量的乘積
    for (auto ch: G[v]) {
        if (ch == p) continue;
        dp1[v] = dp1[v] * (dp1[ch] + dp2[ch]) % MOD;
        dp2[v] = dp2[v] * dp1[ch] % MOD;
    }
}

int main() {
    // 輸入
    int N;
    cin >> N;
    Graph G(N);
    for (int i = 0; i < N - 1; ++i) {
        int u, v;
        cin >> u >> v;
        --u, --v;
        G[u].push_back(v);
        G[v].push_back(u);
    }

    // 以頂點0為根進行搜尋
    dp1.assign(N, 1), dp2.assign(N, 1);
    rec(G, 0);
    cout << (dp1[0] + dp2[0]) % MOD << endl;
}
```

　

# 18.3 (將最大穩定集合問題以整數規劃進行)

準備與圖的各頂點 v 對應的 0-1 變數 x[v]。當穩定集合中包含頂點 v 時，設 x[v]= 1，如果不包含時，則為x[v]= 0。首先，將想要最大化的目標函數設為 sum_v x[v]（對於 v 的 x[v] 的總和）。

然後，限制條件可以考慮為：對於圖的各邊e =（u，v），必須滿足 x[u] + x[v] <= 1。

　

# 18.4 (裝箱問題)

藉由貪婪演算法（First Fit 法）進行「對各物品 i，按順序檢查箱子，將該物品 i 裝進第一個能夠裝入物品 i 的箱子」，設被使用的箱子數量為M。

此時，對於 M 個箱子之中的 M-1 個箱子，會呈物品裝了一半以上容量的狀態。這是因為，如果有 2 個或更多的箱子只裝了一半以下的容量，則它們的內容物可以合併到一個箱子中。這與 First Fit 法的操作相矛盾。

因此，令最佳解為 OPT 時，2 OPT > M - 1會成立。另外，由於 OPT 和 M - 1 為整數，因此推導出 2 OPT >= M。這代表 First Fit 法是 2-近似演算法。

