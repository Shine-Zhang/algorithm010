学习笔记

第七周：

本周的知识相对较多;

1. 字典树和并查集
   - 字典树，分为了字典树的实现，和基于字典树的单词搜索(作为键值的条件)
   - 并查集，相对来说比较简单一些，主要掌握，并查集中路径压缩的代码即可
2. 高级搜索
   - 主要是能够根据实际情况，在回溯时，对于不必要的情况，进行剪枝，从而提高代码效率
   - 双向BFS的使用，相当于从两边向中间搜索
   - 启发式搜索，也叫A*，和广度优先遍历最大的区别是，在搜索的时候，是根据优先级进行搜索，主要是需要掌握，估值函数的实现。
3. AVL和红黑树
   - AVL的性质以及，插入，删除结点的平衡调整
   - 红黑树，性能和AVL同一数量级，以及红黑树的5个特性。
   - 红黑树和AVL，在存储，查找，删除，插入方面的比较