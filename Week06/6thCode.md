因为只有星期天，一天时间，做完实战题，实在没时间做更多的作业了。

1. 编辑距离

```java
class Solution {
    public int minDistance(String word1, String word2) {

        int m = word1.length();
        int n = word2.length();

        // 如果有一个字符串为空串，则编辑举例就是向空串，插入非空串的长度次
        if (m * n == 0) return m + n;

        int[][] dp = new int[m + 1][n + 1];

        for (int i = 0; i <= n; i++) {
            dp[0][i] = i;
        }

        for (int i = 0; i <= m; i++) {
            dp[i][0] = i;
        }

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                // 给word1插入一个字符
                int first = dp[i][j - 1] + 1;
                // 给word2插入一个字符
                int second = dp[i - 1][j] + 1;
                // 修改word1的一个字符
                int third = dp[i - 1][j - 1];
                // 如果当前字符不相等，那就需要多一步，修改word1的一个字符
                if (word1.charAt(i - 1) != word2.charAt(j - 1)) third++;

                dp[i][j] = Math.min(Math.min(first, second), third);  

            }
        }

        return dp[m][n];

    }
}
```

------

2. 最小路径和

```java
class Solution {
    public int minPathSum(int[][] grid) {
        if (grid.length == 0) return 0;
        int m = grid.length;
        int n = grid[0].length;
    
        int[] dp = new int[n];
        dp[0] = grid[0][0];  //（0,0）点的最小和就是其本身
         //初始化第一行
        for (int i = 1; i < dp.length; i++) {
            dp[i] = dp[i - 1] + grid[0][i];
        }
   
        for (int i= 1; i < m; i++) {
            // dp[i][0] = dp[i - 1][0] + grid[i][0] 只能从上面走下来
            dp[0] = dp[0] + grid[i][0]; 
            for (int j = 1; j < n; j++) {
                // dp[i][j] = min(dp[i - 1][j] + dp[i][j - 1]) + grid[i][j]
                dp[j] = Math.min(dp[j], dp[j - 1]) + grid[i][j];
            }
        }

        return dp[n - 1];

    }
}
```

