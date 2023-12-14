# 14.1 (EDPC G - Longest Path)

求解圖的最長路徑乍看之下很困難。然而，注意到此次給定的圖是DAG的話，可以直接應用第 14.4 節中解說的「DAG 上的動態規畫法」。

求解 DAG 上的動態規畫法的狀況下，求最短路徑和最長路徑時，可以使用相同的轉移式來解。

有關具體演算法的詳細內容，記於下面文章的小節「G 問題 - Longest Path」。

[Educational DP Contest 的 F ～ J 問題的解說和類似題目集](https://qiita.com/drken/items/03c7db44ccd27820ea0d)

　

# 14.2 (ABC 061 D  - Score Attack)

這是一個應用 Bellman-Ford 法的練習題。雖然看似為非常簡單的典型問題，但需要注意當答案為 `inf` 時的判斷。有關具體的演算法，記於下面的文章。

[AtCoder ABC 061 D - Score Attack](https://drken1215.hatenablog.com/entry/2019/02/16/075900)

　

# 14.3 (ABC 132 E - Hopscotch Addict)

在「長度為 3 的倍數的路徑」限制下，求解最短路徑的問題。在這種問題中，對給定的圖，思考將頂點倍增以擴展圖形會起效用。有關具體的演算法，記於下面的文章。

[AtCoder ABC 132 E - Hopscotch Addict](https://drken1215.hatenablog.com/entry/2019/07/01/111500)

　

# 14.4 (ARC 005 C - 器具損壞！高橋同學)

可以視為加權圖上的最短路徑問題，加權圖中，移動到牆壁格子的成本為 1，移動到通道格子的成本為 0 。使用 Dijkstra 法解答的計算複雜度為 O(HW (log H + low W))。

然而，對於像此問題這樣，邊的權重只有 0 和 1 的狀況來說，可以應用一種通常稱為 0-1 BFS 的更快演算法，計算複雜度為 O(HW)。有關具體的演算法，記於下面的文章。

[AtCoder ARC 005 C - 器具損壞！高橋同學](https://drken1215.hatenablog.com/entry/2021/07/30/024800)

　

# 14.5 (ABC 077 D - Small Multiple)

這是一個具有非常簡單設定且令人印象深刻的問題。將問題轉化為最短路徑問題的方式也非常巧妙。請嘗試體會解答問題的樂趣。有關具體的演算法，記於下面的文章。

[AtCoder ABC 077 D - Sumall Multiple](https://drken1215.hatenablog.com/entry/2021/07/30/160000)

　





