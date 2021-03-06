# 在D天内送达包裹的能力



- 问题描述

  传送带上的包裹必须在 D 天内从一个港口运送到另一个港口。

  传送带上的第 i 个包裹的重量为 weights[i]。每一天，我们都会按给出重量的顺序往传送带上装载包裹。我们装载的重量不会超过船的最大运载重量。

  返回能在 D 天内将传送带上的所有包裹送达的船的最低运载能力。




- 问题分析

  已知运载能力处于一个范围内，且包裹为一个整体，不可拆分，因此最小为：max(weights)；而最大为：sum(weights)。

  因此可以将问题转化为二分查找问题，左右边界已知，判断条件为：

  ​	当运载能力为mid时，需要times天可以送达全部包括，此时times>D，则说明mid小于实际所需运载能力，因此left = mid + 1；而times <= D，说明mid大于等于实际所需运载能力，因此right = mid；最后left = right = 所求运载能力。

  

- 问题解答

  ```c++
  class Solution {
  public:
      int shipWithinDays(vector<int>& weights, int D) {
          int left = *max_element(weights.begin(), weights.end());
          int right = accumulate(weights.begin(), weights.end(), 0);
          while (left < right) {
              int mid = (right - left)/2 + left;
              int times = 1;
              int cur = 0;
              for (int weight : weights) {
                  if (cur + weight > mid) {
                      times ++;
                      cur = 0;
                  }
                  cur += weight;
              }
               if (times > D)
                  left = mid + 1;
              else
                  right = mid;
          }
  
          return right;
      }
  };
  ```
---
  # 1011. 在D天内送达包裹的能力-wk

- 问题描述

  > 传送带上的包裹必须在 D 天内从一个港口运送到另一个港口。
  >
  > 传送带上的第 i 个包裹的重量为 weights[i]。每一天，我们都会按给出重量的顺序往传送带上装载包裹。我们装载的重量不会超过船的最大运载重量。
  >
  > 返回能在 D 天内将传送带上的所有包裹送达的船的最低运载能力。 

- 问题分析

  - 看似DP，实际上是一个搜索问题

  - 已知最低肯定是要装下最重的物品，而最高自然就是一次性全装完，因此承重的上下界可以得到

  - 然后根据承重和货物重量来维护一个天数，如果所需天数小于D，说明当前承重太大，可以进一步缩减承重，如果所需天数大于D，说明当前承重太小，需要增加权重。

  - 不难发现这是一个上下界确定的单调性搜索问题，因此可以考虑使用二分搜索来优化复杂度

    

- 问题实现

```java
class Solution {
    public int shipWithinDays(int[] weights, int D) { 
        int left = Arrays.stream(weights).max().getAsInt(), right = Arrays.stream(weights).sum();
        while (left < right) {
            int mid = (left + right) / 2; 
            int need = 1, cur = 0;
            for (int weight : weights) {
                if (cur + weight > mid) {
                    ++need;
                    cur = 0;
                }
                cur += weight;
            }
            if (need <= D) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }
}
```



