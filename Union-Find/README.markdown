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

因为和树打交道，这里用了递归的方法。

Recall that each set is represented by a tree and that the index of the root node serves as the number that identifies the set. We're going to find the root node of the tree that the element we're searching for belongs to, and return its index.

回顾一下，每个集合用一棵树来表示，根节点的索引值为集合的代表值。查找该元素所属树的根节点，并返回它的索引值。

1. 第一步先检查输入的 index 值是否是根节点。（根节点的 `parent` 指回自己），如果是，结束查找。
2. Otherwise we recursively call this method on the parent of the current node. And then we do a **very important thing**: we overwrite the parent of the current node with the index of root node, in effect reconnecting the node directly to the root of the tree. The next time we call this method, it will execute faster because the path to the root of the tree is now much shorter. Without that optimization, this method's complexity is **O(n)** but now in combination with the size optimization (covered in the Union section) it is almost **O(1)**.
3. 否则，需要递归
3. We return the index of the root node as the result.

Here's illustration of what I mean. Let's say the tree looks like this:

![BeforeFind](Images/BeforeFind.png)

We call `setOf(4)`. To find the root node we have to first go to node `2` and then to node `7`. (The indices of the elements are marked in red.)

During the call to `setOf(4)`, the tree is reorganized to look like this:

![AfterFind](Images/AfterFind.png)

Now if we need to call `setOf(4)` again, we no longer have to go through node `2` to get to the root. So as you use the Union-Find data structure, it optimizes itself. Pretty cool!

There is also a helper method to check that two elements are in the same set:

```swift
public mutating func inSameSet(_ firstElement: T, and secondElement: T) -> Bool {
  if let firstSet = setOf(firstElement), let secondSet = setOf(secondElement) {
    return firstSet == secondSet
  } else {
    return false
  }
}
```

Since this calls `setOf()` it also optimizes the tree.

## Union (Weighted)

The final operation is **Union**, which combines two sets into one larger set.

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

Here is how it works:

1. We find the sets that each element belongs to. Remember that this gives us two integers: the indices of the root nodes in the `parent` array.

2. Check that the sets are not equal because if they are it makes no sense to union them.

3. This is where the size optimization comes in (Weighting). We want to keep the trees as shallow as possible so we always attach the smaller tree to the root of the larger tree. To determine which is the smaller tree we compare trees by their sizes.

4. Here we attach the smaller tree to the root of the larger tree.

5. Update the size of larger tree because it just had a bunch of nodes added to it.

An illustration may help to better understand this. Let's say we have these two sets, each with its own tree:

![BeforeUnion](Images/BeforeUnion.png)

Now we call `unionSetsContaining(4, and: 3)`. The smaller tree is attached to the larger one:

![AfterUnion](Images/AfterUnion.png)

Note that, because we call `setOf()` at the start of the method, the larger tree was also optimized in the process -- node `3` now hangs directly off the root.

Union with optimizations also takes almost **O(1)** time.

## Path Compression
```swift
private mutating func setByIndex(_ index: Int) -> Int {
    if index != parent[index] {
        // Updating parent index while looking up the index of parent.
        parent[index] = setByIndex(parent[index])
    }
    return parent[index]
}
```
Path Compression helps keep trees very flat, thus find operation could take __ALMOST__ in __O(1)__

## Complexity Summary

##### To process N objects
| Data Structure                          | Union                    | Find                     |
| --------------------------------------- | ------------------------ | ------------------------ |
| Quick Find                              | N                        | 1                        |
| Quick Union                             | Tree height              | Tree height              |
| Weighted Quick Union                    | lgN                      | lgN                      |
| Weighted Quick Union + Path Compression | very close, but not O(1) | very close, but not O(1) |

##### To process M union commands on N objects
| Algorithm                               | Worst-case time |
| --------------------------------------- | --------------- |
| Quick Find                              | M N             |
| Quick Union                             | M N             |
| Weighted Quick Union                    | N + M lgN       |
| Weighted Quick Union + Path Compression | (M + N) lgN     |

## See also

See the playground for more examples of how to use this handy data structure.

[Union-Find at Wikipedia](https://en.wikipedia.org/wiki/Disjoint-set_data_structure)

*Written for Swift Algorithm Club by [Artur Antonov](https://github.com/goingreen)*, *modified by [Yi Ding](https://github.com/antonio081014).*