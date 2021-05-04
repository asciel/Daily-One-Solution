# 粉刷房子III

- 问题描述

  在一个小城市里，有 m 个房子排成一排，你需要给每个房子涂上 n 种颜色之一（颜色编号为 1 到 n ）。有的房子去年夏天已经涂过颜色了，所以这些房子不需要被重新涂色。

  我们将连续相同颜色尽可能多的房子称为一个街区。（比方说 houses = \[1,2,2,3,3,2,1,1\] ，它包含 5 个街区  \[\{1\},\{2,2\}, \{3,3\}, \{2\}, \{1,1\}\] 。）

  给你一个数组 houses ，一个 m * n 的矩阵 cost 和一个整数 target ，其中：

  houses\[i\]：是第 i 个房子的颜色，0 表示这个房子还没有被涂色。
  cost\[i\]\[j\]：是将第 i 个房子涂成颜色 j+1 的花费。
  请你返回房子涂色方案的最小总花费，使得每个房子都被涂色后，恰好组成 target 个街区。如果没有可用的涂色方案，请返回 -1 。



- 问题分析

  dp\[i\]\[j\]\[k\]表示i号房子刷j号颜色、且\[0,i]号房子组成k个街区条件下的最小花费（用花费=INF表示不可能达成， 为了使得INF+INF<=INT_MAX,，设置INF = 0X3f3f3f3f）。

  - 首先，当k为0或者k>i皆为无效状态；

  - 如果houses\[i\]没有粉刷过(houses\[i\] == 0)：dp\[i\]\[j\]\[k\] = min{dp\[i-1\]\[j\]\[k\],  dp\[i-1\]\[a\]\[k-1\] for a != j} + cost\[i\]\[j-1\]
  - 如果houses\[i\]粉刷过，只有j = houses\[i\]时需要求值，其它皆为INTX_MAX，此时无需加i号房的花费：dp\[i\]\[j\]\[k\] = min{dp\[i-1\]\[j\]\[k\] ,  dp\[i-1\]\[a\]\[k-1\] for a != j}
  - 最后，判断不同颜色方案下，最优的方案。



- 问题解答

  ```c++
  const int INF = 0x3f3f3f3f;
  int dp[105][25][105];
  class Solution {
  public:
      int minCost(vector<int>& houses, vector<vector<int>>& cost, int m, int n, int target) {
          // 当k为0皆为无效状态
          for (int i = 0; i <= m; i ++) {
              for (int j = 0; j <= n; j ++) {
                  dp[i][j][0] = INF;
              }
          }
  
          for (int i = 1; i <= m; i ++) {
              int color = houses[i - 1];
              // 当前第i个房间的颜色，注意i是从1开始的。
              for (int j = 1; j <= n; j ++) {
                  for (int k = 1; k <= target; k ++) {
                      // 如果已划分街区数超过已到达房间数是无效的
                      if (k > i) {
                          dp[i][j][k] = INF;
                          continue;
                      }
                      // 第i个房间已经上色
                      if (color != 0) {
                          // 仅有j = color才有效
                          if (j == color) {
                              int cur = INF;
                              // 与上个房间颜色不同
                              for (int c = 1; c <= n; c ++) {
                                  if (c != j) {
                                      cur = min(cur, dp[i - 1][c][k - 1]);
                                  }
                              }
                              // 与上个房间颜色相同，无需粉刷花费
                              dp[i][j][k] = min(dp[i - 1][j][k], cur);
                          } else {
                              dp[i][j][k] = INF;
                          }
                      } else {
                          // 第i个房间没有上色
                          // 粉刷第i个房间为颜色j的开销，j是从1开始的
                          int u = cost[i - 1][j - 1];
                          int cur = INF;
                          for (int p = 1; p <= n; p ++) {
                              // 与上一个房间颜色不同时
                              if (p != j) {
                                  cur = min(cur, dp[i - 1][p][k - 1] + u);
                              }
                              // 与上一个房间颜色相同时
                              dp[i][j][k] = min(cur, dp[i - 1][j][k] + u);
                          }
                      }
                  }
              }
          }
          int ans = INF;
          // 不同颜色方案下，最优的方案
          for (int i = 1; i <= n; i ++) {
              ans = min(ans, dp[m][i][target]);
          }
          return ans == INF ? -1 : ans;
      }
  };
  ```

  

