# 制作m束花所需的最少天数

- 问题描述

  给你一个整数数组 bloomDay，以及两个整数 m 和 k 。

  现需要制作 m 束花。制作花束时，需要使用花园中 相邻的 k 朵花 。

  花园中有 n 朵花，第 i 朵花会在 bloomDay[i] 时盛开，恰好 可以用于 一束 花中。

  请你返回从花园中摘 m 束花需要等待的最少的天数。如果不能摘到 m 束花则返回 -1 。




- 问题分析

  1. 判断无效条件，当可制作的花束数量（总花朵数除以k）小于m时，返回-1.

  2. 可以对天数进行二分查找，由于都得等花盛开才能用，而花与花之间的盛开时间是互不影响的，因此最低天数和最高天数分别是花盛开的最短时间和最长时间。

     

- 问题解答

  ```c++
  class Solution {
  public:
      int minDays(vector<int>& bloomDay, int m, int k) {
          if (m > bloomDay.size() / k) {
              return -1;
          }
          int low = INT_MAX, high = 0;
          int length = bloomDay.size();
          for (int i = 0; i < length; i++) {
              low = min(low, bloomDay[i]);
              high = max(high, bloomDay[i]);
          }
          while (low < high) {
              int days = (high - low) / 2 + low;
              if (canMake(bloomDay, days, m, k)) {
                  //该天数可以满足要求，大于等于所需天数
                  high = days;
              } else {
                  //该天数小于所需天数，天数需增大
                  low = days + 1;
              }
          }
          return low;
      }
  
      bool canMake(vector<int>& bloomDay, int days, int m, int k) {
          //给定可用天数days，制作花束需要k朵花
          int bouquets = 0; //给定days能做的花束数量
          int flowers = 0; //制作本花束用的花朵数
          int length = bloomDay.size();
          for (int i = 0; i < length && bouquets < m; i++) {
              if (bloomDay[i] <= days) { //小于这个天数才能用这朵花
                  flowers++;
                  if (flowers == k) {
                      bouquets++;
                      flowers = 0;
                  }
              } else {
                  flowers = 0;
              }
          }
          return bouquets >= m;
      }
  };
  ```

  ---
  
  # [1482. 制作 m 束花所需的最少天数](https://leetcode-cn.com/problems/minimum-number-of-days-to-make-m-bouquets/)-wk

- 问题描述

  > 给你一个整数数组 bloomDay，以及两个整数 m 和 k 。
  >
  > 现需要制作 m 束花。制作花束时，需要使用花园中 相邻的 k 朵花 。
  >
  > 花园中有 n 朵花，第 i 朵花会在 bloomDay[i] 时盛开，恰好 可以用于 一束 花中。
  >
  > 请你返回从花园中摘 m 束花需要等待的最少的天数。如果不能摘到 m 束花则返回 -1 。 

- 问题分析

  - 该题和”D天内送达包裹“的思路框架非常相似，隐藏边界就是最长的等待时间和最短的等待时间，然后使用二分搜索的方法找到最优解
  - 二分的思想就是，如果给定时间(mid)足够，那么就将区间向小的方向偏移，如果给定时间不足，那么就将区间向大的方向偏移
  - 二分框架已经得到，那么剩下的就是条件函数如何写
  - 根据题意，每一束花需要k朵位置连续的花来制作。因此在统计的时候可以使用贪心的做法，直接统计当前位置的花是否能在mid时间内开花，如果能，就让计数器+1，如果不能，那么就让计数器归零。当计数器达到k的时候，将花束+1，然后计数器重新归零。

- 问题代码

  ```java
  class Solution {
      private int flowers(int n, int []bloomDay, int mid, int k) {
          int cnt = 0;
          int tmp = 0;
          for (int i = 0;i < n; ++i) {
              if (bloomDay[i] <= mid) {
                  cnt ++;
              } else {
                  cnt = 0;
              }
              if (cnt == k) {
                  cnt = 0;  
                  ++tmp;
              }
          }
          return tmp;
      }
      
      public int minDays(int[] bloomDay, int m, int k) {
          int n = bloomDay.length;
          if (m * k > n) return -1;
          int high = Arrays.stream(bloomDay).max().getAsInt();
          int low = Arrays.stream(bloomDay).min().getAsInt();
          while (low < high) {
              int mid = low + (high - low) / 2;
              int tmp = flowers(n, bloomDay, mid, k);
              if (tmp >= m) {
                  high = mid;
              } else {
                  low = mid + 1;
              }
          }
          return low;
          
      }
  }
  ```

  

