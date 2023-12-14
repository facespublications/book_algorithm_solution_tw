# 2.1

可以表示如下。

<img src=https://github.com/drken1215/book_algorithm_solution/blob/master/fig/sol2-1.png height=210mm>



　

# 2.2

計算出滿足 0 <= i < j < k < N 的 (i, j, k) 之組合的數量即可。這等於從 N 個物品中選擇 3 個時的數量，因此如下所示。

<img src=https://github.com/drken1215/book_algorithm_solution/blob/master/fig/sol2-2.png height=60mm>

因此，計算複雜度可評估為 O(N^3) 。



　

# 2.3

計算出滿足p × p <= N 的p的個數即可。因為p <= √N，所以計算複雜度可評估為O(√N)。



　

# 2.4

A小姐的年齡選項可透過 1 次提問而減半。例如在A小姐年齡的選項有 2^5 種時，如下所示透過5次提問，A小姐的年齡選項會變為 1 種。



- 初始狀態：2^5 = 32種選項
- 第1次提問後：2^4 = 16 種選項
- 第2次提問後：2^3 = 8 種選項
- 第3次提問後：2^2 = 4 種選項
- 第4次提問後：2^1 = 2 種選項
- 第5次提問後：2^0 = 1 種選項 (被限縮到只有 1 種的狀態)



同樣地，當A小姐的年齡選項有 2^k 種可能時，可以藉由 k 次提問縮小到1種可能性。想要嚴謹地證明以上內容的話，可以使用數學歸納法。


　

# 2.5

首先，思考使用整數 k 來表示 N = 2^k 的情況（例如 N = 1024 等）。此時，請注意 k = log N。由問題 2.4，已經顯示可以透過 k 次提問來猜中答案，因此得知可在 O(log N) 次提問內猜中答案。

對於一般的整數 N ，進行如下思考。首先，注意將整數 k 定為滿足下列條件。

<img src=https://github.com/drken1215/book_algorithm_solution/blob/master/fig/sol2-5-1.png height=70mm>

例如，當 N = 100 時，k = 7（2^6 = 64，2^7 = 128）。在這種情況下，由於k < log N + 1，因此 k = O(log N)。另一方面，因為 N <= 2^k，所以可以透過 k 次提問猜中答案。從上述可知，透過 O(log N) 次提問，可以猜中A小姐的年齡。



　

# 2.6

根據下圖，以下的等式成立：

<img src=https://github.com/drken1215/book_algorithm_solution/blob/master/fig/sol2-6-2.png height=75mm>

這表示 1 + 1/2 + … + 1/N = O(log N)。


　

<img src=https://github.com/drken1215/book_algorithm_solution/blob/master/fig/sol2-6-1.png width=500mm>

　

