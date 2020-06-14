1th week作业

1. 旋转数组

```java
class Solution {
    public void rotate(int[] nums, int k) {

        int realDigit = k % nums.length;

        //先将整个数组逆序
        reverseArray(nums, 0, nums.length - 1);
        //在将[0, k - 1]个位置的数逆序
        reverseArray(nums, 0, realDigit - 1);

        // 最后将[k, length - 1]逆序
        reverseArray(nums, realDigit, nums.length - 1);

    }

    private void reverseArray(int[] nums, int start, int end) {
      
       int mid = (end - start + 1) / 2;
       for (int i = 0; i < mid; i++) {
          int tmp;
          tmp = nums[start + i];
          nums[start + i] = nums[end - i];
          nums[end - i] = tmp;
       }
    }
}
```

2.  加一

```java
class Solution {
    public int[] plusOne(int[] digits) {
   
    // 从后往前遍历，因为加法运算从右往左加，即从低位到高位
    for (int i = digits.length - 1; i >= 0; i--) {
            digits[i]++;
            digits[i] = digits[i] % 10;
            // 若低位没有进位，则高位的数字都不变直接返回
            if (digits[i] != 0) return digits;
        }
        // 否则，意味着 +1之后，原数多了一位，此时只有一种情况，就是99...9的这种情况
        // +1之后的结果，只能是最高位为1，其余各位都为0
        digits = new int[digits.length + 1];
        digits[0] = 1;
        return digits;
    }
}
```

