#### 二叉树的最近公共祖先

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) {
            return root;
        }

        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);

        return left == null ? right : right == null ? left : root;
    }
}
```

------

```java
利用前序中序序列，构造二叉树
class Solution {

    Map<Integer, Integer> map = new HashMap<>();

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        if (preorder.length == 0) {
            return null;
        }
        for (int i = 0; i < inorder.length; i++) {
            map.put(inorder[i], i);
        }
        return build(preorder, 0, preorder.length - 1, inorder, 0, inorder.length - 1);

    }

    private TreeNode build (int[] preorder, int preStart, int preEnd, int[] inorder,
     int inStart, int inEnd) {
         if (preStart > preEnd) {
             return null;
         }
         int root = preorder[preStart];

         int rootIndex = map.get(root);

         int l = rootIndex - inStart;
         TreeNode left = l == 0 ? null : 
                     build(preorder, preStart + 1, preStart + l, inorder, inStart, rootIndex - 1);
         int r = inEnd - rootIndex;
         TreeNode right = r == 0 ? null : 
                     build(preorder, preStart + l + 1, preEnd, inorder, rootIndex + 1, inEnd);
         TreeNode rootNode = new TreeNode(root);
         rootNode.left = left;
         rootNode.right = right;
         return rootNode;
    }
}
```

------

# 组合

```java
class Solution {

    List<List<Integer>> result = new LinkedList<>();
    public List<List<Integer>> combine(int n, int k) {
        if (n < k) return new ArrayList<>(); 
        List<Integer> single = new ArrayList<>();
        doCombine(n, k, 1, single);
        return result;
    }

    private void doCombine(int n, int k, int start,  List<Integer> single) {
        int size = single.size();
        if (size == k) {
            result.add(new LinkedList(single));
            return;
        }

        // i 的有效极限值满足： n - i + 1 = (k - single.size())
        int end = n - (k - single.size()) + 1;
        for (int i = start; i <= end; i++) {
            single.add(i);
            doCombine(n, k, i + 1, single);
            single.remove(single.size() - 1);
        }
    }
}
```

------

# 全排列

```java
class Solution {
    List<List<Integer>> result;
    public List<List<Integer>> permute(int[] nums) {
        if (nums.length == 0) {
          return new ArrayList<>();
        }

        result = new LinkedList<>();
        doPremute(nums, 0);
        return result;
    }


    private void doPremute(int[] nums, int index) {
        if(index == nums.length - 1){
            List<Integer> list  = new LinkedList<>();
            for(int n : nums) list.add(n);
            result.add(list);
            return;
        }

        for (int i = index; i < nums.length; i++){
            swap(nums, i, index);
            doPremute(nums, index + 1);
            swap(nums, i, index);
        }
    }
    void swap(int[] nums, int i, int j){
        int t = nums[i];
        nums[i] = nums[j];
        nums[j] = t;
    }
}
```

