# 10.1

如下圖所示，葉以外的任何頂點都只有「右邊的子頂點」的二元樹的高度為 N - 1。

<img src=https://github.com/drken1215/book_algorithm_solution/blob/master/fig/sol10-1.png width=400mm>

　

# 10.2

[6, 5, 1] 。

　

# 10.3

[7, 6, 4, 2, 5, 1, 3] 。

　

# 10.4

設強平衡二元樹的頂點數為 N。此時，若樹的高度為 h，則以下不等式會成立。

　　2^h <= N <= 2^(h + 1) - 1  

在此，不等式的左側評估為「高度為 h - 1 的完全二元樹，再添加有一個頂點的二元樹」，而右側的評估表示「高度為 h 的完全二元樹」。因此，可知下式成立。

　　log N - 1 < h <= log N  

藉此可評估 h = O(log N) 。

　

# 10.5

可以透過數學歸納法來證明頂點數為 N 的樹其邊數為 N - 1 。

首先當 N = 1 時，顯然有「具有 1 個頂點」的圖，且其邊數為 0 。

在頂點數為 N 的樹中，選擇一條邊 e。由於樹不包含迴路，當刪除邊 e 時，樹被分成兩個圖，且任一個連通分量都為樹。假設這兩個樹的頂點數分別為 a 和 b（a + b = N）。根據歸納法的假設，每個樹的邊數分別為 a - 1 和 b - 1。因此，得證原始樹的邊數為 (a - 1) + (b - 1) + 1 = a + b - 1 = N - 1。


