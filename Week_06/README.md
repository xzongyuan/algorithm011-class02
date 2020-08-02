最小路径和 题解
### DFS暴力解题思路
DFS比较符合正常人的解题思路，虽然已经试过其它解法，但觉得还是有必要梳理下DFS思路。
有一个用例没通过。当有上万个元素时，会超时。
后面根据暴力解思路优化。
但尽量简化了代码逻辑。
  以cur为起点，进行递归。
  1. nums[cur]获取最大跳数
  2. 遍历1~nums[cur]次，计算next的位置
  3. 剪枝条件：如果minSteps[next] <= minSteps[cur] + 1;  已有方案更好，不需要递归了
  4. 终止条件：可选
    if (minSteps[cur] > minSteps[nums.length - 1]) {
        return;
    }

### 代码

```java
public class Solution {
    int[] minSteps;

    public int jump(int[] nums) {
        if (nums == null || nums.length <= 1) return 0;
        minSteps = new int[nums.length];
        Arrays.fill(minSteps, Integer.MAX_VALUE);

        minSteps[0] = 0;
        dfs(nums, 0);
        return minSteps[nums.length - 1];
    }

    private void dfs(int[] nums, int cur) {
        for (int next = cur + 1; next < nums.length && next <= cur + nums[cur]; next++) {
            if (minSteps[next] <= minSteps[cur] + 1) continue; // 剪枝。当前的方案比已有的方案还差，就不要递归了

            minSteps[next] = minSteps[cur] + 1;
            dfs(nums, next); // 递归尝试所有跳数
        }
    }
}
```
### 贪心算法+BFS解题思路
DFS会超时，原因是每个元素都递归，有很多重复判断。
优化办法自然是避免每个元素都遍历，所以会想到BFS：
只统计每一层最远距离，如果某一层最大index覆盖了最后一个元素，则当前层就是最小跳数。
只需O(N)复杂度就能算出每一层的最大index

代码实现与DFS差异很大，但思路和DFS是一致的，可以理解为BFS。
1. step统计当前是哪一层。
2. end判断当前层最大index，nums[0]是第0层，maxJump、end为0。
3. 当达到当前层最大值end，刷新已经搜索到的最大index maxJump
    刷新step++；表示即将进入下一层。
    判断end是否大于nums.length - 1. 如果是，说明本层(step)已经可以跳到最大元素

```java
class Solution {
   public int jump(int[] nums) {
        if (nums == null || nums.length <= 1) return 0;

        int maxJump = 0;
        int step = 0;
        int end = 0;
        for (int i = 0; i <= nums.length - 2; i++) { // 不需要遍历最后一个元素
            maxJump = Math.max(maxJump, i + nums[i]);
            if (i == end) {
                step++;
                end = maxJump;
                if (end >= nums.length - 1) break;
            }
        }
        return step;
    }
}
```