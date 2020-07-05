

括号生成(广度优先遍历)

```java
class Solution {

    /*
       按照广度优先的思想，求生成括号问题的解：
       1. 可以将括号生成过程的每一个中间状态，看成是一颗二叉树的结点
          (因为每次要么选择左括号，要么选择右括号)
       2. 每一个中间状态，包含3个主要数据: 左括号的数量，右括号的数量，当前的括号字符串
       3. 那么，括号生成问题的解，可以看成是，求一颗高度为n的二叉树的，合法叶节点的问题
          所谓合法，是指合法的括号字符串

    */
    public List<String> generateParenthesis(int n) {
        if (n == 0) return new LinkedList<>();
        List<String> result = new LinkedList<>();
        Queue<Node> queue = new LinkedList<>();
        // 初始状态节点入队列
        queue.offer(new Node(1, 0, "("));

        while (!queue.isEmpty()) {
            Node node = queue.poll();
            
            // 如果已经到了叶节点，则该叶节点就是一个符合条件的结果
            if ( node.left == n && node.right == n) {
                result.add(node.curStr);
            }

            if (node.left < n) {
               queue.add(new Node(node.left + 1, node.right, node.curStr + "(")); 
            }

            if (node.right < node.left) {
               queue.add(new Node(node.left , node.right + 1, node.curStr + ")")); 
            }
        }
        return result;
    }


    static class Node {
        // 记录当前这个状态节点，左括号的数量
        int left;

        // 记录当前这个状态节点，右括号的数量
        int right;

        // 当前的括号字符串
        String curStr;

        public Node (int left, int right, String curStr) {
            this.left = left;
            this.right = right;
            this.curStr = curStr;
        }
        
    }
}
```

柠檬水找零

```java
// 完全模拟找零过程即可，遇到20元的时候，如果有10元，优先用10元找零
class Solution {
    public boolean lemonadeChange(int[] bills) {

        int five = 0;
        int ten = 0;
        for (int i = 0; i < bills.length; i++) {
            switch (bills[i]) {
                case 5:
                    five++;
                    break;
                case 10:
                    if(five == 0) return false;
                    five--;
                    ten++;
                    break;
                case 20:
                    if(five == 0 || (five < 3 && ten == 0)) return false;
                    if (ten > 0) {
                        ten--;
                        five--;
                    } else {
                        five -= 3;
                    }


            }
        }

        return true;

    }
}
```

模拟行走机器人

```java
class Solution {
    // 四个方向分别看做是：上，左，下，右，一次逆时针(向左转)旋转90度
    int[] dx = {0, -1, 0, 1 };
    int[] dy = {1, 0, -1, 0 };
    public int robotSim(int[] commands, int[][] obstacles) {

        Set<Long> set = new HashSet<>();
        int x = 0, y = 0;

        //参照官方题解，将左表转化为一个整数
        for (int i = 0; i < obstacles.length; i++) {
            long ox = obstacles[i][0] + 30000;
            long oy = obstacles[i][1] + 30000;
            set.add((long)(ox << 16) + oy);
        }

        int directionIndex = 0;
        int result = 0;
        for (int i = 0; i < commands.length; i++) {
            switch (commands[i]) {
                case -2:
                    // 左转
                    directionIndex = (directionIndex + 1) % 4;
                    break;
                case -1:
                     // 右转
                     directionIndex = (directionIndex + 3) % 4;
                     break;
                default:
                    for (int j = 1; j <= commands[i]; j++) {
                        int nx = x + dx[directionIndex];
                        int ny = y + dy[directionIndex];
                        long code = ((long)(nx + 30000) << 16) + (long)ny + 30000;
                        if (set.contains(code)) break;
                        x = nx;
                        y = ny;
                        result = Math.max(result, x * x + y * y);

                    }

            }
        }

        return result;
        
    }
}
```

单词接龙

```java
class Solution {

    /* 基于双端队列的官渡优先遍历，求解单词接龙问题
       1. 从两个方向，在字典中搜索，即 beginWord -> endWord 和 endWord -> beginWord
       2. 每次，搜索，选择队列中元素较少的那个队列开始搜索。
       3. 一旦发现，当前搜索到的结点，其在字典中的，一个满足条件的孩子，如果该满足条件
          的孩子结点，已经在另一个方向的搜索中被访问过了，此时，就已经得到结果
          (即两个方向搜索的交点)。
    */
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
 
        int end = wordList.indexOf(endWord);
        if (end == -1) {
            return 0;
        }
        wordList.add(beginWord);
        LinkedList<String> startCur = new LinkedList<>();
        startCur.add(beginWord);
        LinkedList<String> endCur = new LinkedList<>();
        endCur.add(endWord);

        boolean[] startVisited = new boolean[wordList.size()];
        startVisited[startVisited.length - 1] = true;
        boolean[] endVisisted = new boolean[wordList.size()];
        endVisisted[end] = true;
        LinkedList<String> next;

        int level = 0;
        while (startCur.size() > 0 && endCur.size() > 0) {
            level++;
            if (startCur.size() > endCur.size()) {
                LinkedList<String> tmpCur = startCur;
                startCur = endCur;
                endCur = tmpCur;

               boolean[] tmp = startVisited;
               startVisited = endVisisted;
               endVisisted = tmp; 
            }

            next = new LinkedList<>();
            int len = startCur.size();
            for (int i = 0; i < len; i++) {
                for (int j = 0; j < wordList.size(); j++) {
                    if (startVisited[j]) continue;
                    String s = wordList.get(j);
                    if (!check(startCur.get(i), s)) continue;
                    // 当发现，当前访问的结点，是一个合法结点(即)
                    if (endVisisted[j]) return level + 1;
                    startVisited[j] = true;
                    next.add(s);

                } 
            }

            startCur = next;
        }

        return 0;
    }

    private boolean check(String a, String b) {
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

岛屿数量

```java
class Solution {
    int count;
    public int numIslands(char[][] grid) {
        if (grid.length == 0) return 0;

        int count = 0;
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[0].length; j++) {
                if (grid[i][j] == '1') {
                    count++;
                    search(grid, i, j);
                }
            }
        }

        return count;
        

    }

    private void search(char[][] grid, int x, int y) {
        if(x < 0 || x >= grid.length || y < 0 || y >= grid[0].length || grid[x][y] == '0') return;

        grid[x][y] = '0';
        search(grid, x - 1, y);
        search(grid, x + 1, y);
        search(grid, x, y + 1);
        search(grid, x, y - 1);
    }
}
```

扫雷游戏

```java
class Solution {

    int[] dx = {-1, -1, 0, 1, 1, 1, 0, -1}; // 相邻位置
    int[] dy = {0, 1, 1, 1, 0, -1, -1, -1};
    public char[][] updateBoard(char[][] board, int[] click) {
        int x = click[0];
        int y = click[1];
        // 点击到地雷，只会在开始时发生一次，所以直接判断就好
        if (board[x][y] == 'M') {
            board[x][y] = 'X';
            return board;
        }

        solve(board, x, y);
        return board;

    }

    public void  solve (char[][] board, int x, int y) {
        // 超出边界，或者不是未挖出的空方块，则返回
        if (x < 0 || y < 0 || x >= board.length || y >= board[0].length || board[x][y] !='E') return;
        
        // 先将其当做是周围没有地雷的，空方块
        board[x][y] = 'B';
        // 实际判断该方块周围实际有没有地雷相邻
        int m = countAroundM(board, x, y);
        if (m != 0) {
            board[x][y] = (char)(m + '0');
        } else {
            // 如果是空方块，那么递归挖掘，周围相连的空放量
            for (int i = 0; i < 8; i++) {
                solve(board, x + dx[i], y + dy[i]);
            }
        }

        return;

    } 

    // 获取当前节点相邻的地雷数量
    public int countAroundM(char[][] board, int x, int y) {
        int r = board.length;
        int c = board[0].length;
        int count = 0;
        for (int i = 0; i < 8; i++) {
            int newX = x + dx[i];
            int newY = y + dy[i];
            if (newX < 0 || newX >= r || newY < 0 || newY >= c) {
                continue;
            }
            if (board[newX][newY] == 'M') {
                count++;
            }
        }
        return count;
    }


}
```

跳跃游戏

```java
class Solution {
    public boolean canJump(int[] nums) {
        int end = nums.length - 1;
        // 从后往前判断，如果有从前一个节点可以到达当前的end，
        //那么就继续往前判断，前一个节点是否是可达的,最终只要能找到第0个位置，即
        // 从第0个位置触发，是可以到达最后一个位置的
       for (int i = end; i >= 0 ; i--) {
           if(i + nums[i] >= end) {
               end = i;
           }
       }

       return end == 0; 
    }
}
```

搜索旋转排序数组

```java
class Solution {
    public int search(int[] nums, int target) {

        int l = 0;
        int r = nums.length - 1;

        while (l <= r) {
            int mid = (r - l) / 2 + l;
            if (nums[mid] == target) return mid;

            if (nums[mid] < nums[r]) {
                // mid 到 r之间是升序
                if (target > nums[mid] && target <= nums[r]) {
                    // 目标值在升序范围内
                    l = mid + 1;
                } else {
                    // 否则，不在右边的升序序列中，只能在l 到 mid这段
                    r = mid - 1;
                }
            } else {
                // 说明 mid  到 right这块是整体无序的，那l 到 mid一定是有序的
                if (target >= nums[l] && target < nums[mid]) {
                    r = mid - 1;
                } else {
                    //否则，目标值在
                    l = mid + 1;
                }


            }
        }

        return -1;

    }
}
```

