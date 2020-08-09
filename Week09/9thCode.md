9thCode

同构字符串

```java
class Solution {
public boolean isIsomorphic(String s, String t) {
        if(s.length()==0){
            return true;
        }
        return isMap(s,t) && isMap(t,s);
    }

    /*
        判断s中的字符是否唯一映射到了t中的某个字符
    */
    public boolean isMap(String s, String t) {
        int sLen = s.length();
        int[] map =new int[128];
        char[] schars =s.toCharArray();
        char[] tchars = t.toCharArray();
        for(int i = 0; i < sLen; i++){
            if(map[schars[i]] == 0){
                map[schars[i]] = tchars[i];
            }else {
                if(map[schars[i]] != tchars[i]){
                    return false;
                }
            }
        }
        return true;
    }
}
```

最长上升子序列

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        if (nums.length == 0) {
            return 0;
        }
        // dp[i]表示已nums[i]结尾的最大上升子序列
        int[] dp = new int[nums.length];
        dp[0] = 1;
        int result = 1;
        for (int i = 1; i < nums.length; i++) {
            // 以nums[i]结尾的最大上升子序列的个数
            int iMax = 0;
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) {
                    iMax = Math.max(iMax, dp[j]);
                }
            }
            // 在以nums[j]为结尾的最大上升子序列的基础上+1，若没有nums[i] > nums[j]
            // 那么nums[i]自成一个上升子序列，iMax初值为0，+1也没有问题
            dp[i] = ++iMax;
            result = Math.max(result, iMax);
        }

        return result;

    }
}
```

解码方法

```java
class Solution {
    public int numDecodings(String s) {
        char[] chars = s.toCharArray();
        if (chars[0] == '0') return 0;
        // 第0位置非‘0’字符，只有一种解码方式
        int pre = 1;
        int cur = 1;
        for (int i = 1; i < chars.length; i++) {
            // 暂存到当前字符为止的解码方式总数
            int tmp = cur;
            if (chars[i] == '0') {
                // 若果当前字符为0，且其前一个字符为1或为2，则
                // chars[i] z只能和 chars[i - 1]，斗则无法解码，即解码失败
                if (chars[i - 1] != '1' && chars[i - 1] != '2') return 0;
                cur = pre;
            } else if (chars[i - 1] == '1' || (chars[i - 1] == '2'
             && (chars[i] >= '1' && chars[i] <= '6'))) {
            // 如果chars[i] 不是‘0’
            // a. chars[i - 1]为‘1’，则chars[i]既可以单独解码也可以和chars[i - 1]联合解码
            // b. chars[i - 1]为‘2’，且chars[i]为‘1’-‘6’既可以单独解码也可以和chars[i - 1]联合解码
                 cur = pre + cur;
             }

             pre = tmp; 
        }

        return cur;

    }
}
```

atoi

```java
class Solution {
    public int myAtoi(String str) {

    // 符号位默认是正数
    int index = 0, sign = 1, total = 0;

    // 这里要注意先trim在判断
    if(str.trim().length() == 0) return 0;
    //略过前面的空格，找到第一个非空格字符
    while(str.charAt(index) == ' ' && index < str.length())
        index ++;
    //若第一个字符是符号位，则先处理符号位
    if(str.charAt(index) == '+' || str.charAt(index) == '-'){
        sign = str.charAt(index) == '+' ? 1 : -1;
        index ++;
    }
    
    //每计算新的一位之前，都要根据符号位判断，是否会溢出，没溢出就正常计算
    // 一旦溢出，直接就给整数上下界的值
    while(index < str.length()){
        int digit = str.charAt(index) - '0';
        if(digit < 0 || digit > 9) break;
       
        if(Integer.MAX_VALUE / 10 < total ||            
        	Integer.MAX_VALUE / 10 == total && Integer.MAX_VALUE % 10 < digit)
            return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;
        total = 10 * total + digit;
        index++;
    }
    return total * sign;

    }
}
```

最长回文子串

```java
class Solution {
    public String longestPalindrome(String s) {

        int m = s.length();
        if (m == 0) return "";
        // dp[i][j] 的含义是 s的i到j的部分是否是一个回文串
        boolean[][] dp = new boolean[m][m];
        int max = 0;
        int start = 0;
        int end = 0;
        for (int i = m - 1; i >= 0; i--) {
            for (int j = i; j < m; j++) {
                dp[i][j] = s.charAt(i) == s.charAt(j) && (j - i < 3 || dp[i + 1][j - 1]);
                if (dp[i][j] && (j - i + 1 > max)) {
                    max = j - i + 1;
                    start = i;
                    end = j;
                }; 
            }
        }

        return s.substring(start, end + 1);
        
    }
}
```

最长有效括号

```java
class Solution {
    public int longestValidParentheses(String s) {
        int len = s.length();
        if (len == 0) return 0;

        //以第i个位置的括号结尾的括号字符串中包含的最长有效括号数
        int[] dp = new int[len];
        int max = 0;
        char[] target = s.toCharArray();
        for (int i = 1; i < len; i++) {
            if (target[i] == ')') {
                // 当前是右括号的时候
                if (target[i - 1] == '(') {
                    // 若前一个是左括号，则有效的括号长度为
                    // i - 2个位置的有效括号长度 + 1
                    dp[i] = (i >= 2 ? (dp[i - 2]) : 0) + 2;
                } else if (i - dp[i - 1] > 0 && s.charAt(i - dp[i - 1] - 1) == '(') {
            // 若第i-1个位置是右括号，但是在以第i-1个位置的右括号结尾的最长右括号，的前一个位置不是0
            // 且其为左括号，则dp[i] = dp[i - 1] + dp[i - dp[i - 1] - 2]
                    dp[i] = dp[i - 1] + (i - dp[i - 1] >= 2 ? dp[i - dp[i - 1] - 2] : 0) + 2; 

                }
                max = Math.max(dp[i], max);
            }
        }

        return max;

    }
}
```

不同的子序列

```java
class Solution {

/* 用dp[i][j]表示：s的前 i 个字符（下标0到 i-1）有dp[i][j]种方法变为t的前 j 个字符（下标0到 j-1）

1. s[i-1]==t[j-1]时，计算两类情况的和：
 a。保留s[i-1]，有dp[i-1][j-1]种方法。
 b.不保留s[i-1]，有dp[i-1][j]种方法。
2. 故s[i-1]==t[j-1]时，dp[i][j] = dp[i-1][j-1]+dp[i-1][j]。
  s[i-1]!=t[j-1]时，有dp[i-1][j]种方法。

*/

    public int numDistinct(String s, String t) {
       int[][] dp = new int[s.length() + 1][t.length() + 1];
       // 从空串到S的前j个字符组成的字符串，只有1中方法    
       for (int j = 0; j <= s.length(); j++) dp[j][0] = 1;

       for (int i = 1; i <= s.length(); i++) {
           for (int j = 1; j <= t.length(); j++) {
                dp[i][j]=dp[i - 1][j];
                if(s.charAt(i - 1)==t.charAt(j - 1)){
                    dp[i][j] +=dp[i-1][j-1];
                }
           }
       }

       return dp[s.length()][t.length()];
        
    }
}
```

