---
layout: post
title: 数据结构和算法（06）：跳表
date: 2019-06-05 01:02:06.000000000 +09:00
---
1. 结构：链表 + 多级索引

![](https://github.com/CaoTouChan/ctc_imgs/raw/master/006tNc79ly1g4htuc9qnbj30i70almy5.jpg)

2. 时间复杂度：O(log n)

考虑下 **查询** 数据的时间复杂度。

第 k 级索引的节点数是第 k-1 级的 1/2 ，那第 k 级索引的节点个数就是 
$$\frac{n}{2^k}$$
 。

假设索引有 h 级，最高级索引有 2 个节点，那么有 
$$\frac{n}{2^h} = 2$$
，即 
$$ h = log_2 n-1$$
 。故包含原始链表层整个高度是 
$$log_2 n$$
。如果每层遍历 m 个节点，则跳表中查询一个数据的时间复杂度就是 
$$O(m * logn)$$
。

按照上图的结构来讲，每层只需要遍历 3 个节点。（假设我们要查找的数据是 x，在第 k 级索引中，我们遍历到 y 结点之后，发现 x 大于 y，小于后面的结点 z，所以我们通过 y 的 down 指针，从第 k 级索引下降到第 k-1 级索引。在第 k-1 级索引中，y 和 z 之间只有 3 个结点（包含 y 和 z），所以，我们在 K-1 级索引中最多只需要遍历 3 个结点，依次类推，每一级索引都最多只需要遍历 3 个结点。）

![](https://github.com/CaoTouChan/ctc_imgs/raw/master/006tNc79ly1g4hturpb8sj30g2068t97.jpg)

所以，在跳表中查询任意数据的时间复杂度就是 
$$O(logn)$$
。

对于跳表，动态的 **插入、删除** 操作时间复杂度也是 
$$O(logn)$$
。

![](https://github.com/CaoTouChan/ctc_imgs/raw/master/006tNc79ly1g4htuzpvf4j30el0asjrs.jpg)

3. 索引的动态更新

如果在两个索引之间插入过多数据，会导致数据的不平衡。对于红黑树、AVL 树这样平衡二叉树，它们是通过左右旋的方式保持左右子树的大小平衡，而跳表是通过 **随机函数** 来维护前面提到的“平衡性”。

![](https://github.com/CaoTouChan/ctc_imgs/raw/master/006tNc79ly1g4htv7t98aj30fn0ayt9i.jpg)






