# 并查集

并查集数据结构是对一组分成多个不相交的子集元素的处理，并查集又称为不相交集。

到底是神马意思？举个例子，并查集是用来处理下面集合合并和查询：

	[ a, b, f, k ]
	[ e ]
	[ g, d, c ]
	[ i, j ]

这些集合是不相交的，因为它们没有共同的成员。

并查集支持三种基本操作：

1. **Find(A)**：找到 **A** 在那个子集中。比如 `find(d)` 函数返回 `[g, d, c ]` 。 
2. **Union(A, B)**：把某两个集合 **A** 和 **B** 合成一个子集。比如 `union(d, j)` 需要将  `[ g, d, c ]` 和 `[ i, j ]`  合并成一个大的集合`[ g, d, c, i, j ]`。
3. **AddSet(A)**：生成一个只包含 **A** 新子集。如 `addSet(h)` 生成一个新集合 `[ h ]`。

该数据结构最常用于查询无向[图](../Graph/)中的节点。它也能用于提高 Kruskal 算法的效率，用于查找图中最小生成树。

## 代码实现

并查集有很多实现方法，下面用一种相对简单高效的方式实现：加权Quick-Union。



> __PS：可以在 playground 中找到并查集的多种实现方式__

```swift
public struct UnionFind<T: Hashable> {
  private var index = [T: Int]()
  private var parent = [Int]()
  private var size = [Int]()
}
```

这里并查集数据结构实际是森林，每个子集用[树](../Tree/)表示。

由于目标只是保持对每个树节父点的联系，不需要联系子节点，可以通过一个父节点的数组来表示，`parent[ i ]` 表示第 `i` 个父节点。

举例：如果父节点数组像这样

	parent [ 1, 1, 1, 0, 2, 0, 6, 6, 6 ]
	     i   0  1  2  3  4  5  6  7  8

树的结构如下：

	      1              6
	    /   \           / \
	  0       2        7   8
	 / \     /
	3   5   4

森林中有两棵树，每棵对应一组元素集合。（备注：这里是因为受制于 ASCII 表达形式所以用二叉树表示的，实际情况并不局限于此）。

每个子集使用唯一的数字来标示。子集合树的根节点作为索引，如 `1` 是第一棵树的根节点， `6` 是第二棵树的根节点。

在这个例子中有两个子集，第一个代表值为 `1` ，第二个为 `6` 。**Find** 函数返回的是子集的代表值，而不是它的内部数据。

注意 `parent[]` 中根节点指向自己，如 `parent[1] = 1` 和 `parent[6] = 6` ，可以通过这个方法可以发现那些是根节点。

## 添加

从添加开始，看看如何实现一些基本操作：

```swift
public mutating func addSetWith(_ element: T) {
  index[element] = parent.count  // 1
  parent.append(parent.count)    // 2
  size.append(1)                 // 3
}
```

当添加一个新元素，实际是添加一个包含该元素的集合。

1. 保存新元素的索引到 `index` 字典中。可以帮助快速查找该元素。
2. 添加索引到 `parent` 数组中并为这个集合新建一个树。由于这个新集合只包含一个值，而且该值为树的根节点，所以`parent[i]` 指向自己。
3. `size[i]` 是索引值为 `i` 根节点处的节点个数。对于新集合因为只含一个元素所以值为 1 。在随后的合并操作中会用到 `size` 这个数组。

## 查找

经常需要查找某个元素是否在集合中， **Find** 函数就是干这个事滴！在 `并查集` 中又称为 `setof()`: 

```swift
public mutating func setOf(_ element: T) -> Int? {
  if let indexOfElement = index[element] {
    return setByIndex(indexOfElement)
  } else {
    return nil
  }
}
```

先通过 `index` 字典来查找某个元素的索引值，再用一个函数来查找该元素属于哪个集合:

```swift
private mutating func setByIndex(_ index: Int) -> Int {
  if parent[index] == index {  // 1
    return index
  } else {
    parent[index] = setByIndex(parent[index])  // 2
    return parent[index]       // 3
  }
}
```

既然和树打交道了就用递归的方法来解决吧。

回顾一下，每个集合用一棵树来表示，根节点的索引值为集合的代表值。查找该元素所属树的根节点，并返回它的索引值。

1. 第一步先检查输入的 index 值是否是根节点。（根节点的 `parent` 指回自己），如果是，结束查找。
2. 否则，递归调用当前节点的父节点方法。 接下来的是**非常重要的一步**：重写当前节点的父节点为根节点，实际就是将节点重新连接到根节点上。当下次在调用这个方法的时候速度就快了，因为到根节点的路径非常短了。如果没有这个优化这个方法的复杂度为 **O(n)**，但经过路径压缩后（在合并环节）复杂度接近 **O(1)**。
3. 返回根节点作为结果。

这里有一个图形化的解释，让我们来看看：

![BeforeFind](Images/BeforeFind.png)



调用 `setOf(4)` 试试看，为了查到根节点，需要先访问 `2` 节点，然后再访问 `7` 节点。（索引值用红色数字标示）。

调用 `setOf(4)` 后，树结构如下：

![AfterFind](Images/AfterFind.png)



现在若再调用 `setOf(4)` ，不需要再经过节点 `2` 才能到根节点。因此在使用并查集数据结构的时候会自优化，是不是很酷！

这里有个便捷的方法可以判断两个元素是不是在一个集合中：

```swift
public mutating func inSameSet(_ firstElement: T, and secondElement: T) -> Bool {
  if let firstSet = setOf(firstElement), let secondSet = setOf(secondElement) {
    return firstSet == secondSet
  } else {
    return false
  }
}
```

因为调用了 `sefOf()` 方法也会优化树结构。

## 合并（权重）

最后的操作是 `合并` 就是将两个集合合并成一个大的集合。

```swift
    public mutating func unionSetsContaining(_ firstElement: T, and secondElement: T) {
        if let firstSet = setOf(firstElement), let secondSet = setOf(secondElement) { // 1
            if firstSet != secondSet {                // 2
                if size[firstSet] < size[secondSet] { // 3
                    parent[firstSet] = secondSet      // 4
                    size[secondSet] += size[firstSet] // 5
                } else {
                    parent[secondSet] = firstSet
                    size[firstSet] += size[secondSet]
                }
            }
        }
    }
```

计算过程如下：

1. 给出两个集合，这两个集合都有一个根节点的索引值存在 `parent` 数组中。
2. 判断是不是相同的集合，合并两个相同的集合没有任何意义。
3. 以大小作为权重进行优化，如果想让树的深度尽可能的保持最小，需要把小的树添加到大的树上。通过比较两个树的数组个数决定那个树更小。
4. 下面将小一些的树添加到大一些树的根节点。
5. 因为增加一堆新的树节点，需要更新大树的元素个数值。

为了更好介绍这个算法，举个例子说明一下，有两个集合，每个集合的树的数据结构如下：

![BeforeUnion](Images/BeforeUnion.png)

现在调用方法 `unionSetsContaining(4, and: 3)` 将小一些的树添加到大一些的树上：

![AfterUnion](Images/AfterUnion.png)

因为在开始的时候调用了 `setOf()` ，所以大一些的树仍然会走优化流程 — 节点 `3` 直接挂到根节点上。

合并的优化的复杂度也为 **O(1)**。

## 路径压缩
```swift
private mutating func setByIndex(_ index: Int) -> Int {
    if index != parent[index] {
        // Updating parent index while looking up the index of parent.
        parent[index] = setByIndex(parent[index])
    }
    return parent[index]
}
```
路径压缩能够使树不断变平坦，因此查找复杂度 **几乎** 接近 **O(1)**。

## 复杂度总结

##### 处理 N 个元素
| 数据结构                                | Union                    | Find                     |
| --------------------------------------- | ------------------------ | ------------------------ |
| Quick Find                              | N                        | 1                        |
| Quick Union                             | Tree height              | Tree height              |
| Weighted Quick Union                    | lgN                      | lgN                      |
| Weighted Quick Union + Path Compression | very close, but not O(1) | very close, but not O(1) |

##### N个元素中做M次并集

| 算法                                    | 最坏的情况  |
| --------------------------------------- | ----------- |
| Quick Find                              | M N         |
| Quick Union                             | M N         |
| Weighted Quick Union                    | N + M lgN   |
| Weighted Quick Union + Path Compression | (M + N) lgN |

## 更多

可以继续查看[源码](https://github.com/raywenderlich/swift-algorithm-club/tree/master/Union-Find)中的其他算法的工作原理。还可以看[Union-Find at Wikipedia](https://en.wikipedia.org/wiki/Disjoint-set_data_structure)。

*作者[Artur Antonov](https://github.com/goingreen)*, *审核 [Yi Ding ](https://github.com/antonio081014).*  *译者KeithMorning*。