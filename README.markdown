![Swift Algorithm Club](Images/SwiftAlgorithm-410-transp.png)
# 欢迎来到 Swift 算法俱乐部！

> 注：本文译自 [Swift Algorithm Club](https://github.com/raywenderlich/swift-algorithm-club)。
>
> 注：由于早先[swift-algorithm-club-cn](https://github.com/ksco/swift-algorithm-club-cn) 翻译不全且已经停止维护，本项目是在此基础上的进行的翻译和更新，感谢 [ksco](https://github.com/ksco) 及其小伙伴的先前付出
>
> 想体验更好的点[这里](https://keithmorning.github.io/swift-algorithm-club-cn/)

在这里，你可以找到很多流行的算法和数据结构的具体实现，使用的是大家最喜欢的新语言 Swift，并对他们的工作原理配有详细的解释。

如果你是一个计算机学院的学生，为了考试想学习一下算法；又或者你是一个自学成才的程序员，想提高一下自身的理论姿势水平－－你真 TM 来对地方了！

这个项目的目的是**解释各种算法的工作方式**。所以我们主要关注代码的清晰性和可读性，而不是为了产出一个可复用的库，让读者可以直接拖进自己的工程使用。换句话说，绝大多数的代码都是可以用于实际的项目中的，不过需要你根据自己的项目需求进行一些修整。

所有的代码都是兼容 **Xcode 10** 以及 **Swift 4.2** 的。如果 Swift 有更新，我们也会及时跟进。
这个项目目前正在进行中。更多的算法将被加入，敬请期待。:-)


:heart_eyes:**欢迎提供建议和贡献！**:heart_eyes:

## 重要链接

[什么是算法和数据结构？](What%20are%20Algorithms.markdown)－做薄饼！

[为什么要学习算法？](Why%20Algorithms.markdown)－还在担心这不是你的菜吗？请读一下这篇文章。

[大 O 表示法](Big-O%20Notation.markdown)－我们经常会听到这样的话：“这个算法是 O(n) 的”。如果你不知道这是啥意思，请读读这篇文章。

[*算法设计技巧](Algorithm%20Design.markdown)－怎样设计自己的算法？

[欢迎参与翻译！](How%20to%20Contribute.markdown)－如果有意参与翻译，请阅读注意事项！

## 从哪开始？

如果你之前没有接触过算法和数据结构，你可以从下面这些简单易懂的算法开始看起：

- [栈](Stack/)
- [队列](Queue/)
- [插入排序](Insertion%20Sort/)
- [二分搜索](Binary%20Search/)和[二分搜索树](Binary%20Search%20Tree/)
- [归并排序](Merge%20Sort/)
- [Boyer-Moore 字符串搜索算法](Boyer-Moore-Horspool/)

## 算法列表

### 搜索算法

- [*线性搜索](Linear%20Search/)－从数组中查找某个元素。
- [二分搜索](Binary%20Search/)－从已排序的数组中快速查找元素。
- [统计出现次数](Count%20Occurrences/)－统计某个值在数组中的出现次数。
- [查找最大／最小值](Select%20Minimum%20Maximum)－找到数组中的最大／最小值。
- [第 K 大元素](Kth%20Largest%20Element/)－找到数组中的第 **K** 大元素，例如中位数。
- [选取样本](Selection%20Sampling/)－随机地从集合中选取一些元素作为样本。
- [并查集](Union-Find/)－保持一些不相交的集合，帮助你快速合并它们。

### 字符串搜索算法

- [Brute-Force 算法](Brute-Force%20String%20Search/)－一个简单粗暴的方法。
- [Boyer-Moore 算法](Boyer-Moore-Horspool/)－一种高效的字符串子串搜索算法（BM算法）。它不需要对被搜索的字符串中的字符进行逐一比较，而是根据一个查找表跳过其中的某些部分。
- [Knuth-Morris-Pratt 算法](Knuth-Morris-Pratt/) - 一个线性复杂度字符串搜索算法（KMP算法），通过模式字符进行匹配返回所有的字符串的位置
- [Rabin-Karp 算法](Rabin-Karp/) - 通过哈希算法快速搜索
- [最长公共子序列算法](Longest%20Common%20Subsequence/)－找到两个字符串中的最长公共子序列。
- [Z-Algorithm](Z-Algorithm/) 在一个字符串中找到所有的模式字符，并返回模式字符在字符串中的开始位置

### 排序算法

探究排序算法的工作原理是非常有趣的，但在实际的编码中，你几乎永远也不会需要自己编写排序算法，Swift 自带的 `sort()` 函数已经非常够用了，但如果你还是好奇背后的原理，请继续阅读。

基本的排序算法：

- [插入排序](Insertion%20Sort/)
- [*选择排序](Selection%20Sort/)
- [*希尔排序](Shell%20Sort/)

快速的排序算法：

- [*快速排序](Quicksort/)
- [归并排序](Merge%20Sort/)
- [*堆排序](Heap%20Sort/)

混合排序:

- [内省排序](Introsort/)

特殊的排序算法

- [*计数排序](Counting%20Sort/)
- [*基数排序](Radix%20Sort/)
- [*拓扑排序](Topological%20Sort/)

不好的排序算法（知道就行了，不要用！）：

- [*冒泡排序](Bubble%20Sort/)
- [*慢速排序](Slow%20Sort/)

### 压缩算法

- [*变动长度编码法(RLE)](Run-Length%20Encoding/)。将重复的值存储为一个单字节及其计数。
- [*哈夫曼编码](Huffman%20Coding/)。将常见的元素使用更小的单位存储。

### 杂项

- [*搅乱算法](Shuffle/)－随机搅乱数组中的内容。
- [*梳排序](Comb%20Sort/) - 基于冒泡算法后提高的算法
- [*凸包算法](Convex%20Hull/)
- [*米勒-拉宾素性测试](Miller-Rabin%20Primality%20Test/) 这是一个素数吗？
- [*换硬币数量最小问题](MinimumCoinChange/) 一个动态规划的例子
- [*遗传进化算法](Genetic/).  一个简单的例子用来展现一个数值仿照生物进化，如何慢慢突变到自己理想值。
- [* Myers 差分算法](Myers%20Difference%20Algorithm/). 求两个序列的最长公共子序列。
### 数学算法

- [*最大公约数算法(GCD)](GCD/)－特殊福利：最小公倍数算法。
- [*排列组合算法](Combinatorics/)－还记得高中学过排列组合数学吗？
- [*调度场算法](Shunting%20Yard/)－用于将中缀表达式转换为后缀表达式的经典算法。
- [*Karatsuba Multiplication](Karatsuba%20Multiplication/)- 另一种初等乘法
- [*Haversine 距离](HaversineDistance/) 计算球面上两点距离
- [*Strassen 矩阵乘法算法](Strassen%20Matrix%20Multiplication/) 高效处理矩阵乘法的算法
- [*判断顺时针逆时针](/CounterClockWise/) - 多边形的面积问题

### 机器学习


- [*k-Means 聚类算法](K-Means/)－无监督的分类器，将数据聚类为 K 个簇。
- K-近邻算法
- [*线性回归](Linear%20Regression/) 一种建立两个或者更多变量关系模型的技术
- 逻辑回归
- 神经网络
- 网页排名算法
- [*朴素贝叶斯算法](Naive%20Bayes%20Classifier/)
- [*模拟退火算法](Simulated%20annealing/) 是一种通用概率演算法，用来在一个大的搜寻空间内找寻命题的最优解。

## 数据结构

对于特定的任务，数据结构的选择需要基于以下几点考量。

首先，你的数据是具有某种形态的，并且有一些必要的操作方法。如果你想基于关键字来查找对象，需要的是字典类型的数据结构；如果你的数据原生就是分层级的，就需要某种类型的树形结构；而如果你的数据是线性的，则你需要的是数据结构可能就是栈或队列等。

其次，具体的选择还与你在实际使用中最常用的操作方法有关，因为不同的数据结构都对不同的操作方法做了优化。举例来说，如果你经常需要获取集合中的某些较为重要的元素，那么使用堆或优先队列就比普通的数组要好很多。

绝大多数情况下，使用 Swift 内建的 `Array`、`Dictinary`、`Set` 就足够高效了，但某些时候，可能还是需要某些更合适的数据结构...

### 数组变体

- [二维数组](Array2D/)－固定尺寸的二维数组，可用于棋盘游戏。
- [*比特集](Bit%20Set/)－**n** 位大小固定尺度的序列。
- [*固定长度数组](Fixed%20Size%20Array/)－如果你确切的知道数据的大小，使用老式的固定长度的数组会更加高效。
- [*有序数组](Ordered%20Array/)－一个永远有序的数组。
- [*Rootish Array Stack](Rootish%20Array%20Stack/) - 空间时间高效率的Swift数组

### 队列

- [栈](Stack/)－后进先出！
- [队列](Queue/)－先进先出！
- [*双端队列](Deque/)
- [*优先队列](Priority%20Queue)－一个保持最重要的元素总是在最前面的队列。
- [*环形缓冲区](Ring%20Buffer/)－一个语义上的固定大小的环形缓冲区，实际使用的是一维序列头尾相接实现。

### 列表

- [*链表](Linked%20List/)－链接起来的数据序列。包含单向和双向链表。
- [*跳跃表](Skip-List/)- 跳跃表是一种随机化的数据结构与AVL/红黑树有着相同的时间复杂度O(logN),并且更新和搜索更加高效简单。

### 树

- [*树](Tree/)－通用目的的树形结构。
- [*二叉树](Binary%20Tree/)－一种节点最多有两个孩子节点的树形结构。
- [二叉搜索树(BST)](Binary%20Search%20Tree/)－以某种方式组织自己的节点的二叉树，以求较快的查询速度。
- [*红黑树](Red-Black%20Tree/) - 自平衡二叉搜索树
- [*AVL 树](AVL%20Tree/)－一种通过旋转来维持平衡的二叉搜索树。 
- [*伸展树](Splay%20Tree/)- 自平衡二叉搜索树允许快速检索最近更新的节点
- [*线索二叉树](Threaded%20Binary%20Tree/) - 一种二叉搜索树通过额外的数据加快遍历并降低消耗
- [*线段树](Segment%20Tree/)－能够快速地对某区间进行计算。
  - [Lazy Propagation](https://github.com/raywenderlich/swift-algorithm-club/tree/master/Segment%20Tree/LazyPropagation)

- k-d 树
- [稀疏表](Sparse%20Table/) 又一个可以对数组部分做计算的数据结构，但是这次可以更快！
- [*堆](Heap/)－存储在一维数组中的二叉树，所以它不需要使用指针。很适合做为优先队列使用。
- 斐波那契堆
- [*字典树](Trie/) - 一种特殊类型的树结构用来保存关联数据的结构
- [*B 树](B-Tree/) - 自平衡搜索树，每个节点可以超过两个子节点
- [*基数树](Radix%20Tree/)是将指针与long整数键值相关联的机制，它存储有效率，并且可快速查询
- [*四叉树](QuadTree/) 
- [*八叉树](Octree/)

### 哈希

- [*哈希表](Hash%20Table/)－允许你通过一个关键词来存取数据。字典通常都是基于哈希表实现的。
- 哈希函数

### 集合

- [*布隆过滤器](Bloom%20Filter/)－一个常量内存数据结构，用于概率性的检测某个元素是否在集合中。
- [*哈希集合](Hash%20Set/)－使用哈希表实现的集合。
- [*多重集](Multiset/) - 可以重复添加元素的集合
- [*有序集](Ordered%20Set/)－很看重元素顺序的集合。

### 图

- [*图](Graph/)
- [*广度优先搜索(BFS)](Breadth-First%20Search/)
- [*深度优先搜索(DFS)](Depth-First%20Search/)
- [*最短路径算法](Shortest%20Path%20%28Unweighted%29/)－作用对象为无权值树。
- [*单源最短路径算法](Single-Source%20Shortest%20Paths%20(Weighted)/)
- [*最小生成树](Minimum%20Spanning%20Tree%20%28Unweighted%29/)－作用对象为无权值树。
- [*最小生成树](Minimum%20Spanning%20Tree/)- 作用对象为有权值的树
- [*任意两点间的最短路径算法](All-Pairs%20Shortest%20Paths/)
- [*Dijkstra's 最短路径算法](Dijkstra%20Algorithm/)
- [A* 算法](A-Star/)


## 智力题

很多程序员在面试时都会被问到一些算法性质的智力题。这里只囊括了一点比较有趣的。想了解更多的智力题（及答案），请浏览[这里](http://elementsofprogramminginterviews.com/)，还有[这里](http://www.crackingthecodinginterview.com)。

- [*二和问题](Two-Sum%20Problem/)
- [*三和问题/四和问题](3Sum%20and%204Sum/)
- [*Fizz Buzz](Fizz%20Buzz/)
- [*蒙提霍尔问题](Monty%20Hall%20Problem/)
- [*寻找回文串](Palindromes/)
- [*哲学家进餐问题](DiningPhilosophers/)
- [*扔🥚问题](Egg%20Drop%20Problem/)
- [*编码和解码二叉树](Encode%20and%20Decode%20Tree/)
- [*最近点对算法](Closest%20Pair/)

## Raywenderlich 书籍推荐

喜欢本系列？可以购买 Swift 算法学院出品的 [Data Structures & Algorithms in Swift](https://store.raywenderlich.com/products/data-structures-and-algorithms-in-swift)。

![Data Structures & Algorithms in Swift Book](Images/DataStructuresAndAlgorithmsInSwiftBook.png)

你可以从像链表，队列，栈这些基本的数据机构开始，学习如何用 Swift 语言的方式实现他们。然后进阶实现各种类型的树，以及各种特定用途的树如二叉树， AVL 树，二分查找树和字典树等。

学习比冒泡，插入排序更好的算法，如归并排序、基数排序、堆排序和快速排序。学习构建有向图和无向图以及带权重的图来表示现世界中的模型，用广度优先算法、深度优先算法、Dijkstra Prim 算法高效的遍历图和树，在网络中找出最短路径和使用最低代价。

在书的最后，用数据结构和算法实战解决一些通用问题，这样你就踏上自己开发高效算法的历程啦！


放个链接在此，[raywenderlich.com store](https://store.raywenderlich.com/products/data-structures-and-algorithms-in-swift).

## 学无止境！

请参阅以下书籍获取更多内容：

- [Introduction to Algorithms](https://mitpress.mit.edu/books/introduction-algorithms) by Cormen, Leiserson, Rivest, Stein
- [The Algorithm Design Manual](http://www.algorist.com) by Skiena
- [Elements of Programming Interviews](http://elementsofprogramminginterviews.com) by Aziz, Lee, Prakash
- [Algorithms](http://www.cs.princeton.edu/~rs/) by Sedgewick
- [Grokking Algorithms](https://www.manning.com/books/grokking-algorithms) by Aditya Bhargava

下面的书籍均可在网上**免费**阅读：

- [Algorithms](http://www.beust.com/algorithms.pdf) by Dasgupta, Papadimitriou, Vazirani
- [Algorithms, Etc.](http://jeffe.cs.illinois.edu/teaching/algorithms/) by Erickson
- [Algorithms + Data Structures = Programs](http://www.ethoberon.ethz.ch/WirthPubl/AD.pdf) by Wirth
- Algorithms and Data Structures: The Basic Toolbox by Mehlhorn and Sanders
- [Open Data Structures](http://opendatastructures.org) by Pat Morin
- [Wikibooks: Algorithms and Implementations](https://en.wikibooks.org/wiki/Algorithm_Implementation)

其它关于算法的资源：

- [EKAlgorithms](https://github.com/EvgenyKarkan/EKAlgorithms)－非常棒的使用 Objective-C 编写的算法集合。
- [@lorentey](https://github.com/lorentey/)－使用 Swift 实现的产品级质量的常用算法和数据结构实现。
- [Rosetta Code](http://rosettacode.org)－提供了很多中语言的算法实现。
- [AlgorithmVisualizer](http://jasonpark.me/AlgorithmVisualizer/)－在浏览器中的图形化算法演示。
- [Swift Structures](https://github.com/waynewbishop/SwiftStructures) 在哪里使用这些数据结构 [这里](http://waynewbishop.com/swift)

## 声明

Swift算法俱乐部最初是由 [Matthijs Hollemans](https://github.com/hollance) 创建的。
现在由 [Vincent Ngo](https://www.raywenderlich.com/u/jomoka), [Kelvin Lau](https://github.com/kelvinlauKL) 和 [Richard Ash](https://github.com/richard-ash) 进行维护.

Swift算法俱乐部由 [算法贡献者](https://github.com/rwenderlich/swift-algorithm-club/graphs/contributors) 和[raywenderlich.com](https://www.raywenderlich.com)社区大力支持。 我们一直在寻找联盟者，为什么不[加入](.github/CONTRIBUTING.md)呢？:]


## 许可(License)

本项目(包括[原项目](https://github.com/raywenderlich/swift-algorithm-club))都是基于 MIT 协议的

我们所有提交的 pull request 都是通过这个平台，所有代码和文章因此都是遵循该许可的。 根据该许可，Razeware, LLC 和其他人都对相关的文档保留权利。你可以[这里](https://github.com/raywenderlich/swift-algorithm-club/blob/master/LICENSE.txt)找到许可文档。

> 所有中文文档的翻译来自[Swift 算法学院](https://github.com/raywenderlich/swift-algorithm-club)，因此也将遵循原项目协议
