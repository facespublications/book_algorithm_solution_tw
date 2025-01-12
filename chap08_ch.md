# 8.1

評估以下程式碼的計算複雜度。因為進行了將 N 個節點依序查過的處理，所以計算複雜度可估為O(N)。

　

```cpp
void printList() {
    Node* cur = nil->next;
    for (; cur != nil; cur = cur->next) {
        cout << cur->name << " -> ";
    }
    cout << endl;
}
```

　

# 8.2

以下的程式碼中，`get(i)` （從 head 開始取得第 i 個元素的函數）會分別檢查 i 個節點。因此，以下程式碼的計算時間與下式成比例，計算複雜度為 O(N^2) 。

　　1 + 2 + ... + N = N (N + 1) / 2


另外，此程式碼最終會依序查看 N 個節點，但與8.1的處理相比，計算複雜度變差。

　

```cpp
for (int i = 0; i < N; ++i) {
    cout << get(i) << endl;
}
```

　

# 8.3

準備一個變數（在此設為 `s`），用來表示鏈結串列的大小。在鏈結串列初始化時，初始化為`s = 0;` 。

並且，在新的元素被插入到鏈結串列時，以 `++s` 將 `s` 增加；而當鏈結串列中的元素被刪除時，以 `--s` 將 `s` 減少。

此時，變數 `s` 會一直代表「鏈結串列的大小」。

　

# 8.4

在單向鏈結串列中，為了刪除特定節點 v，需要找到 v 的前一個節點 p。如果耗費 O(N) 的計算複雜度也沒問題的話，可以依序查看 N 個節點，線性搜尋出滿足下式的節點 p。

　　`p->next = v`

這樣的話，刪除節點 v 的操作可以如下實現。

　

```cpp
// 已找到要刪除的節點 v 的前一個節點 p，使其轉為整體處理。
void erase(Node *v, Node *p) {
    if (v == nil) return;  // 當 v 是結束節點時，不執行任何操作。
    p->next = v->next;
    delete v;
}
```

　

# 8.5

利用**雜湊表**（在 C++ 中為 `std::unordered_set` 型態，在 Python 中為 `set` 型態）來進行。首先，將 N 個不同的整數 `a[0]`, `a[1]`, ..., `a[N-1]` 插入雜湊表（設為 `H`）。這個操作平均可以用 O(N) 的計算複雜度實現。

接著對每個 i（i = 0, 1, ..., M-1），檢查 `b[j]` 是否被包含於 `H` 中。準備一個用來表示 `a` 和 `b` 中共同整數個數的變數 `counter`（初始化為 0），如果 `b[j]` 被包含於 `H` 中，執行 `counter++;` 這樣的操作使變數 `counter` 增加。這個操作平均可以用 O(M) 的計算複雜度實現。

最後，輸出變數 `counter` 的值。整體而言，這個方法平均可以用 O(N + M) 的計算複雜度來實現。

　

# 8.6

利用藉由雜湊表實作的**關聯陣列**（在 C++ 中為 `std::unordered_map` 型，在 Python 中為 `dict` 型）。對於 N 個整數 `a[0]`, `a[1]`, ..., `a[N-1]`，建立以下的關聯陣列吧。平均可以用 O(N) 的計算複雜度實現。

-----

`D[v]` ← 在數列 `a[0]`, `a[1]`, ..., `a[N-1]` 之中，包含有整數 `v` 的個數

-----

接著，準備一個用來表示答案的變數 `result`（初始值為 0）。對於每個 i（= 0, 1, ..., M-1），進行 `result += D[b[j]];` 這樣將答案累加的操作。這個操作平均以 O(M) 的計算複雜度實現。

最後，輸出變數 `result` 的值。整體而言，平均可以用 O(N + M) 的計算複雜度實現。

　

# 8.7

首先，與問題 8.6 一樣，對 N 個整數 `a[0]`, `a[1]`, ..., `a[N-1]`建立如下的關聯陣列。平均可以用 O(N) 的計算複雜度實現。

-----

`D[v]` ← 布林值（True / False），用來表示數列 `a[0]`, `a[1]`, ..., `a[N-1]` 中是否包含有整數 `v` 。

-----

然後，對每個 i (= 0, 1, ..., N-1)，判斷 `D[K - b[i]]` 是否為 True。這個操作平均可以用 O(N) 的計算複雜度實現。

總體而言，平均可以用 O(N) 的計算複雜度實現。
