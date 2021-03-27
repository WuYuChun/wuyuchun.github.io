# 





MuqSS零代价解决了BFS存在的两个问题：

1. *遍历查找的O(n)问题。* 引入Skiplist数据结构替换双向链表，在*O*(log*n*)的插入代价下将查找的时间复杂度降为O(1)。 【关于Skiplist，可以参考我的另一篇文章： https://blog.csdn.net/dog250/article/details/46997155】 [[2021-03-23-skiplist数据结构]]
2. *多CPU操作全局链表的锁问题。* 引入每CPU链表，避免全局争锁。同时以trylock代替lock，以损失准确性为代价实现无锁操作。



*保持简单* 这个约束下设计了MuqSS，其要点是：

- Skiplist的作用类似主线Linux内核CFS中的红黑树，但比红黑树简单得多。
- 选择task的算法遍历所有CPU的Skiplist表头，选择当前全局最优task。
- 锁粒度细化到每个CPU的Skiplist。
- 遍历过程针对每CPU锁采用trylock，失败则继续下一个CPU，实现无锁化。



我们看一下MuqSS调度算法中选择task的具体操作流程：

![](/home/dji/messi/github/wuyuchun.github.io/images/posts/2021-03-23-BFS和MuqSS-01.png)

和BFS不同，BFS是在全局的链表中遍历找VD最小的task，而MuqSS则遍历每CPU链表的表头查找VD最小的task。

之所以要遍历所有CPU的Skiplist上的表头，是因为每次查找操作均要找到一个全局最优解，这样也就消除了主动[负载均衡](https://cloud.tencent.com/product/clb?from=10680)的必要，极大降低了算法的复杂性。

时间复杂度同样都是O(n)，但MuqSS的n指的是CPU数量而非task数量。我们看一下MuqSS选择task算法相比BFS的改进：

![](/home/dji/messi/github/wuyuchun.github.io/images/posts/2021-03-23-BFS和MuqSS-02.png)