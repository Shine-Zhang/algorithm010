7th week 作业

1. 实现字典树

```java
class Trie {
  // 字典树的根节点
   private TrieNode root;
    /** Initialize your data structure here. */
    public Trie() {
        root = new TrieNode();

    }
    
    /** Inserts a word into the trie. */
    public void insert(String word) {
        TrieNode node = root;
        char[] chars = word.toCharArray();
        for (char c : chars) {
            if (node.children[c - 'a'] == null) {
                node.children[c - 'a'] = new TrieNode();
            }
            node = node.children[c - 'a'];
        }
        // 单词插入完毕，给最后一个节点打标记，表示是一个完整单词。
        node.isEnd = true;

    }
    
    /** Returns if the word is in the trie. */
    public boolean search(String word) {
        TrieNode node = root;
        char[] chars = word.toCharArray();

        for (char c : chars) {
            int index = c - 'a';
            if ((node.children[index] == null)) return false;
            node = node.children[index];
        }

        return node.isEnd;

    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    public boolean startsWith(String prefix) {
        TrieNode node = root;
        char[] chars = prefix.toCharArray();

        for (char c : chars) {
            if ((node.children[c - 'a'] == null)) return false;
            node = node.children[c - 'a'];
        }

        return true;

    }

    private static class TrieNode {
        public static final int BRANCHES = 26;

        // 该节点的所有孩子
        TrieNode[] children = new TrieNode[BRANCHES];;

        // 是否是完整单词
        boolean isEnd;

        public TrieNode() {
        }

    }
}
```

2. 单词搜索II

```java
class Solution {
    
    public List<String> findWords(char[][] board, String[] words) {
        // 根据已有单词构造字典树
        TrieNode root = buildTire(words);
        List<String> result = new LinkedList<>();
        // 表示4连同的方向
        int[][] directrions = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
        int m = board.length;
        if (m == 0) return result;
        int n = board[0].length;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                // 回溯从i,j位置开始，搜索
                doFindWords(board, root, result, i, j, directrions );
            }
        }

        return result;
 
    }

    private void doFindWords(char[][] board, TrieNode root, List<String> result, 
        int i, int j, int[][] directrions) {
        char c = board[i][j];
        if (c == '#' || root.children[c - 'a'] == null) return;
        root = root.children[c - 'a'];
        if (root.word != null) {
            // 找到了单词
            result.add(root.word);
            // 防止重复找到同一个单词
            root.word = null;
        }

        board[i][j] = '#';
        // 从四个方向搜索
        for (int k = 0; k < directrions.length; k++) {
            int row = i + directrions[k][0];
            int col = j + directrions[k][1];
            if (row < 0 || col < 0 || row >= board.length || col >= board[0].length) continue;
            doFindWords(board, root, result, row, col, directrions);
        }

        // 恢复状态
        board[i][j] = c;
    }

    

    private TrieNode buildTire(String[] words) {
        TrieNode root = new TrieNode();

        for (String word : words) {
            TrieNode node = root;
            char[] chars = word.toCharArray();
            for (char c : chars) {
                int index = c - 'a';
                if (node.children[index] == null) 
                    node.children[index] = new TrieNode(); 
                node = node.children[index];

            }
            node.word = word;
        }

        return root;
    }


   static class  TrieNode {
        TrieNode[] children = new TrieNode[26];
        // 到该节点为止，表示了一个完整单词，word记录完整的单词
        String word;
    }

}
```

3. 朋友圈

```java
class Solution {


    public int findCircleNum(int[][] M) {

        int[] parent = new int[M.length];
        // 初始化，并查集，让每个节点先自成一个集合
        for (int i = 0; i < parent.length; i++) {
            parent[i] = i;
        }
        int m = M.length;
        int count = m;
        if (m == 0) return 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < m; j++) {
                if (i != j && M[i][j] == 1) {
                    if(union(parent, i , j)) count--;
                }
            }
        }

        return count;
        
    }

    // 合并集合, 返回值表示是否合并了两个不同的集合
    boolean union(int parent[], int x, int y) {
        int xset = find(parent, x);
        int yset = find(parent, y);
        if (xset == yset) return false;

        parent[xset] = yset;
        return true;      
    }

    // 查找
    int find(int parent[], int x) {
        while (parent[x] != x) {
            // 进行路径压缩
            parent[x] = parent[parent[x]];
            x = parent[x];
        }
        return x;
    }
}
```

4. 最小基因

```java
class Solution {
    public int minMutation(String start, String end, String[] bank) {

        LinkedList<String> cur = new LinkedList<>();
        LinkedList<String> next;

        boolean[] visited = new boolean[bank.length];
        cur.add(start);
        int level = 0;
        while (cur.size() > 0) {
           next = new LinkedList<>();
            // 遍历当前层
            for (String s : cur) {
                if (s.equals(end)) {
                    return level;
                }

                for (int i = 0; i < bank.length; i++) {
                    if (!visited[i] && isNext(bank[i], s)) {
                        visited[i] = true;
                        next.add(bank[i]);
                    } 
                }
            }
      
            level++;
            cur = next;
        }

        return -1;
    }


    private boolean isNext(String a, String b) {
        int diff = 0;
        int len = a.length();
        for (int i = 0; i < len; i++) {
            if(a.charAt(i) != b.charAt(i)) diff++;
            if (diff > 1) break;
        }

        return diff == 1 ? true : false;
    }
}
```

5. N皇后问题

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
        //  从第一行开始，回溯尝试
        doSolve(n, board, 0);
        return result;
    }

    private void doSolve(int n, char[][] board, int row) {
      if (row == n) {
          List<String> one = new LinkedList<>();
          for (int i = 0; i < n; i++) {
              one.add(new String(board[i]));
          }

          result.add(one);
          return;
      }
    
      for (int i = 0; i < n; i++) {
          // 判断，如果在(row, i) 位置放下皇后，和之前的皇后不会相互攻击，则向下一层递归
          if (isValid(board, row, i, n)) {
              board[row][i] = 'Q';
              doSolve(n, board, row + 1);
              board[row][i] = '.';
          }

      }
    }

    // 从横向，竖向，以及两斜线防线检查皇后位置是否合法
     private boolean isValid (char[][] board, int x, int y, int n) {
        for (int i = 0; i < x; i++) {
            if (board[i][y] == 'Q') return false; 
        }


        for (int i = x - 1, j = y + 1; i >=0 && j < n; i--, j++) {
            if (board[i][j] == 'Q') return false; 
        }

        for (int i = x - 1, j = y - 1; i >= 0 && j >= 0; i--, j--) {
            if (board[i][j] == 'Q') return false; 
        }

        return true;
    }
}
```

