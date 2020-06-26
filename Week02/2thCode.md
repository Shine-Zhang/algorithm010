作业题目及对应实现：



1.有效的字母异位词

```java
class Solution {
    public boolean isAnagram(String s, String t) {
         int len1 = s.length();
         int len2 = t.length();
         if (len1 != len2) return false;
         if (len1 == 0) return true; 
        char[] countArray = new char[26];
       
        for (int i = 0; i < len1; i++) {
            countArray[s.charAt(i) - 'a']++;
            countArray[t.charAt(i) - 'a']--;
        }
    
        for (int i = 0; i < 26; i++) {
            if (countArray[i] != 0) return false;
        }

        return true;
    }
}
```

------

2.字母异位词分组

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        if (strs.length == 0 ) {
            return new ArrayList<>();
        }

        int[] table = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29,
                31, 37, 41, 43, 47, 53, 59, 61, 67, 71,
                73, 79, 83, 89, 97, 101};

        Map<Integer, List<String>> map = new HashMap<>();
        List<List<String>> result = new ArrayList<>();
        int len = strs.length;
        for (int i = 0; i < len; i++ ) {
            String value = strs[i];
            // 获取每个字符串对应的key值
            int key = getToken(value, table);
            List<String> list;
            if (map.containsKey(key)) {
                list = map.get(key);
            } else {
                list = new ArrayList<>();
                result.add(list);
                map.put(key, list);
            }
            list.add(value);
        }
        return result;

    }
	
    /*
    	该方法主要用来生成一个字符串对应的，在map中的key值，其生成方式为：
    	1. 每个字符，对应一个不同的质数
    	2. 每个字符串的key值为， 每个字符对应的质数累积的结果
    	之所以可以这样做的原因是，一个数的质因式分解的结果是唯一的
    */
    private int getToken(String src, int[] table) {
        if (src.length() == 0) {
            return 0;
        }
        char[] chars = src.toCharArray();

        int result = 1;
        for (int i = 0; i < chars.length; i++) {
            result *= table[chars[i] - 'a'];
        }

        return result;
    }
}
```

------

3. 二叉树的中序遍历

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        Stack<TreeNode> stack = new Stack<>();
        List<Integer> result = new ArrayList<>();
        TreeNode cur = root;
        while(!stack.isEmpty() || cur != null) {
            while(cur != null) {
                stack.push(cur);
                cur = cur.left;
            }
            
            TreeNode e = stack.pop();
            result.add(e.val);
            if (e.right != null) {
                cur = e.right;
            }
        
        }

        return result;
    }
}
```

------

4. N叉树的层序遍历

```java
这是我的解法，但是看了别人的解法后，还有其他很好的解法

class Solution {
    public List<List<Integer>> levelOrder(Node root) {
        // 由levelStart和levelEnd来确定，一层的结点确切的来说
        // 让后在下一层结点入队列的过程中，有currentIndex来记录下一层，的最后一个节点的位置
        int levelStart = -1;
        int levelEnd = 0;
        int curIndex = 0;
        if (root == null) {
            return new ArrayList<>();
        }

        LinkedList<Node> queue = new LinkedList<>();
        queue.offer(root);
		// 用来存放一层的结点所谓一层就是从（levelStart，levelEnd]
        List<Integer> row = new ArrayList<>();
        List<List<Integer>> result = new ArrayList<>();
        while (!queue.isEmpty()) {
            Node cur = queue.poll();
            List<Node> children = cur.children;
            if (children != null) {
                for (Node n : children) {
                    queue.add(n);
                    curIndex++;
                }
            }
            levelStart++;
            row.add(cur.val);
            if (levelStart == levelEnd) {
                // 当前层遍历完毕，加入最终结果集
                result.add(row);
                row = new ArrayList<>();
                levelEnd = curIndex;
            }
        }

        return result;
    }
}
```

------

5. 丑数

```java
class Solution {
    public int nthUglyNumber(int n) {
        int[] ugly = new int[n];
        int index2 = 0;
        int index3 = 0;
        int index5 = 0;
        ugly[0] = 1;
        for (int i = 1; i < n; i++ ) {
            ugly[i] = min(ugly[index2] * 2, ugly[index3] * 3, ugly[index5] * 5);
            while(ugly[index2] * 2 <= ugly[i]) index2++; 
            while(ugly[index3] * 3 <= ugly[i]) index3++; 
            while(ugly[index5] * 5 <= ugly[i]) index5++; 
        }

        return ugly[n - 1];

    }

    private int min(int a, int b, int c) {
        int tmp = a < b ? a : b;
        return tmp < c ? tmp : c;
    }
}
```

------

6. 前k个高频元素

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {

       HashMap<Integer, Integer> map = new HashMap<>();
	   // 利用hashMap统计频次
       for (int i = 0; i < nums.length; i++) {
           map.put(nums[i], map.getOrDefault(nums[i], 0) + 1);
       }

       // 构造小顶堆
       PriorityQueue<Integer> queue = new PriorityQueue<>((n1, n2) -> map.get(n1) - map.get(n2));

       Set<Integer> keys = map.keySet();
    
       int[] result = new int[k];
       for (Integer e : keys) {
        Integer topMin = queue.peek();
        // 若小顶堆中的元素个数已达k个，且当前元素，比堆顶元素频次低就跳过(因为是频次的小顶堆)
        if (queue.size() == k && map.get(topMin) > map.get(e)) continue;
        // 否则，要么堆中元素个数不满k个，要么当前元素的频次，高于或等于堆顶元素
        queue.add(e);
        if (queue.size() > k) {
            // 删除当前堆中频次最低的元素
            queue.poll();
        }
       }
		
        // 出堆的频次是由低到高的，所以倒着放到数组中
        for (int i = k - 1; i >= 0; i--) {
            result[i] = queue.poll();
        }

        return result;
    }
}
```

