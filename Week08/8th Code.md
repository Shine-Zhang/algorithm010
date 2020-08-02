8th 作业

1.LRU缓存机制

```java
class LRUCache {

    Node head;
    Node tail;
    int size;
    int capacity;

    private Map<Integer, Node> searchCache = new HashMap<>();
    public LRUCache(int capacity) {
        this.capacity = capacity;
        head = new Node(0, 0);
        tail = new Node(0, 0);

        head.next = tail;
        tail.prev = head;
    }
    
    public int get(int key) {
        
        Node node = searchCache.get(key);
        if (node == null) {
            return -1;
        }

        moveToHead(node);
        return node.value;

    }
    
    public void put(int key, int value) {

        Node node = searchCache.get(key);
        if (node == null) {
            // 若缓存中没有
            Node newNode = new Node(key, value);
            searchCache.put(key, newNode);
            headNode(newNode);
            size++;
            if (size > capacity) {
              searchCache.remove(tail.prev.key);  
              removeNode(tail.prev);
              size--;
            }
            return;
        }

        //若缓存中已有
        node.value = value;
        moveToHead(node);

    }

    private void headNode(Node node) {
        node.next = head.next;
        node.prev = head;

        head.next.prev = node;
        head.next = node;        
    }

    private void removeNode(Node node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    private void moveToHead(Node node) {
        removeNode(node);
        headNode(node);
    }

    static class Node {
        int key;
        int value;
        Node prev, next;

        public Node(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }
}
```

2. 合并区间

```java
class Solution {
    public int[][] merge(int[][] intervals) {
        // 先按照区间起始位置排序
        Arrays.sort(intervals, (v1, v2) -> v1[0] - v2[0]);
        // 遍历区间
        int[][] res = new int[intervals.length][2];
        int idx = -1;
        for (int[] interval: intervals) {
            // 如果结果数组是空的，或者当前区间的起始位置 > 结果数组中最后区间的终止位置，
            // 则不合并，直接将当前区间加入结果数组。
            if (idx == -1 || interval[0] > res[idx][1]) {
                res[++idx] = interval;
            } else {
                // 反之将当前区间合并至结果数组的最后区间
                res[idx][1] = Math.max(res[idx][1], interval[1]);
            }
        }
        return Arrays.copyOf(res, idx + 1);
    }
}

```

3. N皇后

```java
class Solution {

    List<List<String>> result; 
    public List<List<String>> solveNQueens(int n) {
       if (n == 0) return new ArrayList<>();

        // 初始化期盼
        char[][] board = new char[n][n];
        for(int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                board[i][j] = '.';
            }
        }
        result = new LinkedList<>();

        int size = (1 << n) - 1;
        //  从第一行开始，回溯尝试
        doSolve(size, board, 0, 0, 0, 0);
        return result;
    }

    private void doSolve(int size, char[][] board, int rowIndex,  int row, int ld, int rd) {
      // 当所有皇后已经都放到棋盘上的时候，此时已经得到了一个解
      if (row == size) {
          List<String> one = new LinkedList<>();
          for (int i = 0; i < board.length; i++) {
              one.add(new String(board[i]));
          }
          result.add(one);
          return;
      }

      int pos  = size & (~(row | ld | rd));

      while (pos  != 0) {
        int p = pos & (-pos);
        pos -= p ;
        int colIndex = mappingColPosition(p);
        board[rowIndex][colIndex - 1] = 'Q';
        doSolve(size, board, rowIndex + 1, row | p, (ld | p) << 1,  (rd | p) >> 1);
        board[rowIndex][colIndex - 1] =  '.';
      }
    }

    // 求一个2的幂次整数的幂次
    public int mappingColPosition(int pos) {
        int count = 0;
        while (pos != 0) {
           pos >>>= 1;
           count++;
        }
        return count;
    }
}
```

4. N皇后II

```java
class Solution {
    int count;
    public int totalNQueens(int n) {
      int size = (1 << n) - 1;
       doSolve(size, 0, 0, 0);
       return count;
    }

    public void doSolve(int size, int row, int ld, int rd) {
        // 找到解，计数器加1
        if (row == size) {
            count++;
            return;
        }

        // 确定该行，还可以放皇后的位置集合
        int pos = size & (~(row | ld | rd));
        // 在该行所有可以放皇后的位置上回溯
        while (pos != 0) {
            // 取一行最低位的1，即最右边的那个可以放皇后的位置
            int p = pos & (-pos);
            // 去掉这个位置
            pos &= (pos - 1);

            //回溯下一层
            doSolve(size, row | p, (ld | p) << 1, (rd | p) >> 1);
        }
    }
}
```

