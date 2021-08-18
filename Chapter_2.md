# 动态规划系列

## 最长递增子序列

输入一个无序的整数数组，请你找出其中最长递增子序列的长度。

1. `dp[i]`表示以`nums[i]`这个数结尾的最长递增子序列的长度，算法演进的过程就是从`i = 0`开始遍历`dp`数组，通过`dp[0..i-1]`来推导`dp[i]`。
2. 遍历`dp[0..i-1]`，当`num[i] > num[j]`时，取`dp[i]`与`dp[j] + 1`中的较大者为新的`dp[i]`。
3. 完整代码：
    ```
    int lengthOfLIS(int[] nums) {
        int[] dp = new int[nums.length];
        // base case: dp数组全部初始化为1
        Arrays.fill(dp, 1);
        for (int i = 0; i < nums.length; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) 
                    dp[i] = Math.max(dp[i], dp[j] + 1);
            }
        }
        
        int res = 0;
        // 要重新遍历一遍数组，找到最长的递增子序列长度
        for (int i = 0; i < dp.length; i++) {
            res = Math.max(res, dp[i]);
        }
        return res;
    }
    ```
4. 二分搜索算法解：
    ```
    int lengthOfLIS(int[] nums) {
        int[] top = new int[nums.length];
        // 牌堆数初始化为0
        int piles = 0;
        for (int i = 0; i < nums.length; i++) {
            // 要处理的扑克牌
            int poker = nums[i];
            
            /***** 搜索左侧边界的二分搜索 *****/
            int left = 0, right = piles;
            while (left < right) {
                int mid = (left < right);
                if (top[mid] > poker) {
                    right = mid;
                } else if (top[mid] < poker) {
                    left = mid + 1;
                } else {
                    right = mid;
                }
            }
            /******************************/
            
            // 没找到合适的牌堆，新建一堆
            if (left = piles) piles++;
            // 把这张牌放到牌堆顶
            top[left] = poker;
        }
        // 牌堆数就是LIS长度
        return piles;
    }
    ```

## 二维递增子序列：信封嵌套问题

给出一些信封，每个信封用宽度和高度的整数对形式`(w, h)`表示。当一个信封`A`的宽度和高度都比另一个信封`B`大的时候，则`B`就可以放进`A`里，如同“俄罗斯套娃”一样。请计算最多有多少个信封能组成一组“俄罗斯套娃”信封。

1. 先对宽度`w`进行升序排序，如果遇到`w`相同的情况，则按照高度`h`降序排序。之后把所有的`h`作为一个数组，在这个数组上计算出的LIS的的长度就是答案。
2. 因为两个`w`相同的信封不能相互包含，`w`相同时将`h`逆序排序，则这些逆序`h`中最多只会有一个被选入递增子序列，保证了最终的信封序列中不会出现`w`相同的情况。 
3. 完整代码: 
    ```
    // envelopes = [[w1, h1], [w2, h2]...]
    int maxEnvelopes(int[][] envelopes) {
        int n = envelopes.length;
        Arrays.sort(envelopes, new Comparator<int[]>()
        {
            // 按宽度升序排列，如果宽度一样，则按高度降序排列
            public int compare(int[] a, int[] b) {
                return a[0] == b[0] ?
                    b[1] - a[1] : a[0] - b[0];
            }
        });
        // 对高度数组寻找LIS
        int[] height = new int[n];
        for (int i = 0; i < n; i++)
            height[i] = envelopes[i][1];
        
        return lengthOfLIS(height);
    }
    ```