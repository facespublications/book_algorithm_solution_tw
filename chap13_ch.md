# 13.1 (圖的連通分量數)

首先，試著以深度優先搜尋求解。

可以透過對本文中的程式碼 13.4 施以一些修改來求解（下述程式碼）。

具體而言，在main 函數中，對所有頂點v呼叫`dfs(v)`，直到對各頂點的`seen[v]` 為true為止。此時，對於`seen[v]`已為true的頂點，不呼叫 `dfs(v)`。

因此，就各連通分量而言，對該連通分量所包含的頂點v只會呼叫一次`dfs(v)` 。所以下述程式碼的變數`result`在演算法結束時，表示「連通分量的數量」。

　

```cpp
#include <iostream>
#include <vector>
using namespace std;

// 表示圖的型態
using Graph = vector<vector<int>>;

// 深度優先搜尋
vector<bool> seen;
void dfs(const Graph &G, int v) {
    // 將頂點 v 設為已訪問
    seen[v] = true;

    // 搜尋與頂點 v 相鄰的各頂點
    for (auto next_v: G[v]) {
        // 已搜尋過的頂點不再搜尋
        if (seen[next_v]) continue;

        // 遞迴地搜尋
        dfs(G, next_v);
    }
}

int main() {
    // 頂點數和邊數
    int N, M;
    cin >> N >> M;

    // 接收圖的輸入
    Graph G(N);
    for (int i = 0; i < M; ++i) {
        int a, b;
        cin >> a >> b;
        G[a].push_back(b);
        G[b].push_back(a);
    }

    // 直到全部頂點都變成已搜尋為止前進行 DFS
    int result = 0;
    seen.assign(N, false);  // 初始化全部頂點為未訪問
    for (int v = 0; v < N; ++v) {
        // 跳過已經搜尋過的連通分量
        if (seen[v]) continue;

        // 以頂點 v 為起點進行DFS
        dfs(G, v);

        // 增加連通分量數
        ++result; 
    }

    cout << result << endl;
}
```
 
　

接下來，讓我們嘗試使用廣度優先搜尋來求解。與深度優先搜尋完全相同，策略是直到全部頂點都變成已搜尋為止前，進行BFS。

　

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

// 表示圖的型態
using Graph = vector<vector<int>>;

// 以頂點 s 為起點的 BFS
vector<int> dist;  // 用於 BFS 的距離陣列
void bfs(const Graph &G, int s) {
    queue<int> que;

    // 初始條件
    dist[s] = 0;
    que.push(s);

    // 在佇列變空為止前進行搜尋
    while (!que.empty()) {
        // 從佇列中取出開頭元素
        int v = que.front();
        que.pop();

        // 搜尋與頂點 v 相鄰的各頂點
        for (auto next_v: G[v]) {
            // 已搜尋過的頂點不再搜尋
            if (dist[next_v] != -1) continue;

            // 將新的頂點加入佇列
            dist[next_v] = dist[v] + 1;
            que.push(next_v);
        }
    }
}

int main() {
    // 頂點數和邊數
    int N, M;
    cin >> N >> M;

    // 接收圖的輸入
    Graph G(N);
    for (int i = 0; i < M; ++i) {
        int a, b;
        cin >> a >> b;
        G[a].push_back(b);
        G[b].push_back(a);
    }

    // 將陣列 dist 全部初始化為-1
    dist.assign(N, -1);

    // 從各頂點開始的BFS
    int result = 0;
    for (int v = 0; v < N; ++v) {
        // 跳過已經搜尋過的連通分量
        if (dist[v] != -1) continue;

        // 以頂點 v 為起點進行DFS
        bfs(G, v);

        // 增加連通分量數 
        ++result; 
    }

    cout << result << endl;
}
```

　

# 13.2 (s-t 路徑)

與深度優先搜尋的解法同樣，進行以頂點 s 為起點的廣度優先搜尋（下方程式碼）。在完成搜尋後，可知如下。

- 當 `dist[t] != -1` 時，表示可以從 s 到達 t 。
- 當 `dist[t] == -1` 時，表示無法從 s 到達 t 。

　

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

// 表示圖的型態
using Graph = vector<vector<int>>;

// 以頂點 s 為起點的BFS
vector<int> dist;  // 用於BFS的距離陣列
void bfs(const Graph &G, int s) {
    queue<int> que;

    // 初始條件
    dist[s] = 0;
    que.push(s);

    // 在佇列變空為止前進行搜尋
    while (!que.empty()) {
        // 從佇列中取出開頭元素
        int v = que.front();
        que.pop();

        // 搜尋與頂點 v 相鄰的各頂點
        for (auto next_v: G[v]) {
            // 已搜尋過的頂點不再搜尋
            if (dist[next_v] != -1) continue;

            // 將新的頂點加入佇列
            dist[next_v] = dist[v] + 1;
            que.push(next_v);
        }
    }
}

int main() {
    // 頂點數、邊數、s和t
    int N, M, s, t;
    cin >> N >> M >> s >> t;

    // 接收圖的輸入
    Graph G(N);
    for (int i = 0; i < M; ++i) {
        int a, b;
        cin >> a >> b;
        G[a].push_back(b);
        G[b].push_back(a);
    }

    // 將陣列 dist 全部初始化為-1，並進行BFS
    dist.assign(N, -1);
    bfs(G, s);

    // 是否能夠到達t
    if (dist[t] != -1) cout << "Yes" << endl;
    else cout << "No" << endl;
}
```

　

# 13.3 (二部圖判定)

與程式碼 13.5 同樣，準備一個管理各頂點顏色的陣列`color`，並反覆進行以下處理。

- 與白色頂點相鄰的頂點塗成黑色
- 與黑色頂點相鄰的頂點塗成白色

在這個過程中，如果發現「兩端點為同色的邊」，則可以確定這不是二部圖。

　

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

// 表示圖的型態
using Graph = vector<vector<int>>;

// 以頂點 s 為起點的 BFS
vector<int> color;  // 1: 黒色, 0: 白色, -1: 未確定
bool bfs(const Graph &G, int s) {
    queue<int> que;

    // 初始條件
    color[s] = 0;
    que.push(s);

    // 佇列變空為止前進行搜尋
    bool is_bipartite = true;
    while (!que.empty()) {
        // 從佇列中取出開頭元素
        int v = que.front();
        que.pop();

        // 搜尋與頂點 v 相鄰的各頂點
        for (auto next_v: G[v]) {
            // 當相鄰頂點的顏色皆已確定
            if (color[next_v] != -1) {
                // 當同樣顏色相鄰時，不是二部圖
                if (color[next_v] == color[v]) {
                    is_bipartite = false;
                }
                continue;
            }
            
            // 改變相鄰頂點的顏色，並推入佇列
            color[next_v] = 1 - color[v];
            que.push(next_v);
        }
    }
    return is_bipartite;
}

int main() {
    // 頂點數和邊數
    int N, M;
    cin >> N >> M;

    // 接收圖的輸入
    Graph G(N);
    for (int i = 0; i < M; ++i) {
        int a, b;
        cin >> a >> b;
        G[a].push_back(b);
        G[b].push_back(a);
    }

    // 將陣列 color 全部初始化為 -1，並進行 BFS
    color.assign(N, -1);
    bool is_bipartite = true;
    for (int v = 0; v < N; ++v) {
        // 當 v 已搜尋過時，不再搜尋
        if (color[v] != -1) continue;

        // 以頂點 v 為起點的  BFS
        // 如果連通分量不是二部圖，則整體答案也是 false
        if (!bfs(G, v)) is_bipartite = false;
    }
    
    if (is_bipartite) cout << "Yes" << endl;
    else cout << "No" << endl;
}
```
		
　

# 13.4 (迷宮的最短路徑)

這裡假設是以如下格式輸入：

- 第 1 行是迷宮的大小（垂直長度，水平長度）
- 第 2 行開始是迷宮的地圖（`S`：起點，`G`：終點，`.`：通道，`#`：牆壁）

使用 BFS 找到從格子 `S` 到格子 `G` 的最短路徑。將各格子視為圖的頂點，將格子的相鄰關係視為圖的邊來進行搜尋。

　

```
8 8
.#....#G
.#.#....
...#.##.
#.##...#
...###.#
.#.....#
...#.#..
S.......
```

　

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <string>
using namespace std;

// 表示朝上下左右移動的向量
const vector<int> dx = {1, 0, -1, 0};
const vector<int> dy = {0, 1, 0, -1};

int main() {
    // 輸入
    int H, W;  // 垂直長度、水平長度
    cin >> H >> W;
    vector<string> field(H);
    for (int i = 0; i < H; ++i) cin >> field[i];

    // 找出起點和終點的格子
    int sx = -1, sy = -1, gx = -1, gy = -1;
    for (int i = 0; i < H; ++i) {
        for (int j = 0; j < W; ++j) {
            if (field[i][j] == 'S') sx = i, sy = j;
            if (field[i][j] == 'G') gx = i, gy = j;
        }
    }

    // 使各頂點以 pair<int,int> 型態表示
    using Node = pair<int,int>;
    queue<Node> que;  // 佇列 

    // 初始條件
    // dist[i][j] 表示到格子 (i, j) 的最短路徑長度
    que.push({sx, sy});
    vector<vector<int>> dist(H, vector<int>(W, -1));
    dist[sx][sy] = 0;

    // 開始 BFS 
    while (!que.empty()) {
        auto [x, y] = que.front();
        que.pop();

        // 依序查看相鄰頂點
        for (int dir = 0; dir < 4; ++dir) {
            int nx = x + dx[dir];
            int ny = y + dy[dir];

            // 跳過超出範圍的部分
            if (nx < 0 || nx >= H || ny < 0 || ny >= W) continue;

            // 不進入牆壁
            if (field[nx][ny] == '#') continue;

            // 如果還未搜尋過，則新增到佇列中
            if (dist[nx][ny] == -1) {
                dist[nx][ny] = dist[x][y] + 1;
                que.push(Node(nx, ny));
            }
        }
    }

    // 最短路徑長度
    cout << dist[gx][gy] << endl;
}
```

　

# 13.5 (拓樸排序)

13.5 和 13.6 題是搜尋的應用。以 BFS 來實現拓樸排序的方法有以下：

- 將匯點（sink，成為終點的頂點）添加到佇列中
- 刪除匯點，藉此將成為新匯點的頂點添加到佇列中

具體的演算法和細節記於下面文章的「第3.4 節 拓樸排序」，請務必查閱參考。

[BFS（廣度優先搜尋）入門！ 〜 熟練地運用佇列 〜](https://qiita.com/drken/items/996d80bcae64649a6580)


　

# 13.6 (有向迴圈檢測)

在存在有向迴圈的有向圖上執行DFS時，可能會出現「當從頂點 v 開始搜尋時，在完成從頂點 v 可以到達的所有頂點的搜尋之前，先回到頂點 v 」的情況。為了檢測這種情況，可以準備以下資料：

- `seen[v]` ← 表示在行進順序中已經訪問過 v（在回歸順序中是否訪問過 v 是不確定的）
- `finished[v]` ← 表示在回歸順序中已經訪問過 v

如果在「`seen[v]` = true && `finished[v]` = false」這種狀態下發生返回頂點 v 的情況，則確定「圖包含迴圈，並且 v 是迴圈中的頂點」。

有關具體的演算法和細節，記於下面文章的「第4.6節 迴圈檢測」，請務必查閱參考。

[DFS（深度優先搜尋）入門！ 〜 前往圖・演算法世界的入口 〜【下篇】](https://qiita.com/drken/items/a803d4fc4a727e02f7ba)


　

