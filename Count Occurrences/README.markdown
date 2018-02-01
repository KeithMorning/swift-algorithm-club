# 计数器

目标：统计数组中某值出现的频率。

最直接的做法就是用 [线性搜索](../Linear%20Search/)从头到尾查一边做一下统计。这个算法复杂度为 **O(n)**。

但是如果是排序数组，把 [二分查找](../Binary%20Search/) 修改一下就能用，复杂度为 **O(log n)** 。

如下数组：

	[ 0, 1, 1, 3, 3, 3, 3, 6, 8, 10, 11, 11 ]
如果需要统计 `3` 的频率，做一次普通的二分查找，结果找到四个 `3` 的索引值：

	[ 0, 1, 1, 3, 3, 3, 3, 6, 8, 10, 11, 11 ]
	           *  *  *  *

但是仍然不知道有多少个 `3` 。为了统计一下 `3` ，还是需要进行从左到右做线性搜索，在大多数情况下很快，但是在最坏的情况比如数组中只有 `3` ， 时间复杂度仍然为 **O(n)**。

技巧是使用两次二分查找， 第一个用于找到 `3` 开始位置（左边界），另一个找打 `3` 的结束位置（右边界）。

代码如下：

```swift
func countOccurrencesOfKey(_ key: Int, inArray a: [Int]) -> Int {
  func leftBoundary() -> Int {
    var low = 0
    var high = a.count
    while low < high {
      let midIndex = low + (high - low)/2
      if a[midIndex] < key {
        low = midIndex + 1
      } else {
        high = midIndex
      }
    }
    return low
  }

  func rightBoundary() -> Int {
    var low = 0
    var high = a.count
    while low < high {
      let midIndex = low + (high - low)/2
      if a[midIndex] > key {
        high = midIndex
      } else {
        low = midIndex + 1
      }
    }
    return low
  }

  return rightBoundary() - leftBoundary()
}
```

注意两个子函数 `lefBoundary()` 和 `rightBoundary()` 与 [二分查找](../Binary%20Search/) 非常类似，不同之处在于他们在查找到目标值后没有停下来，而是继续运行下去。

把下面代码复制到 playground 中运行一下：

```swift
let a = [ 0, 1, 1, 3, 3, 3, 3, 6, 8, 10, 11, 11 ]

countOccurrencesOfKey(3, inArray: a)  // returns 4
```

> **记住:** 如果你要用自定义数组，需要使用已排序的数组。

举个例子看一下查找过程：

	[ 0, 1, 1, 3, 3, 3, 3, 6, 8, 10, 11, 11 ]
从 `low = 0` 到 `high = 12` 查找左边界，第一个 index 值为6：

	[ 0, 1, 1, 3, 3, 3, 3, 6, 8, 10, 11, 11 ]
	                    *

二分查找后不仅要确认 `3` 是否存在，还要确认它在哪里 *第一次* 出现。

因为这个算法规则和二分查找是一样的，现在可以忽略右半部分了，重新计算一下中间位置：

	[ 0, 1, 1, 3, 3, 3 | x, x, x, x, x, x ]
	           *

重复一下后，现在位置为 `3` ， 它确实是最开始位置，但是算法并不知道，这里再二分一次：

	[ 0, 1, 1 | x, x, x | x, x, x, x, x, x ]
	     *

还没结束要再分割一次，这次用右半部分：

	[ x, x | 1 | x, x, x | x, x, x, x, x, x ]
	         *

直到数组再也无法分割就找到了左边的边界，位置为 3 。

开始找数组的右边边界，因为和上面的过程相似这里只给出了遍历过程：

	[ 0, 1, 1, 3, 3, 3, 3, 6, 8, 10, 11, 11 ]
	                    *
	
	[ x, x, x, x, x, x, x | 6, 8, 10, 11, 11 ]
	                              *
	
	[ x, x, x, x, x, x, x | 6, 8, | x, x, x ]
	                           *
	
	[ x, x, x, x, x, x, x | 6 | x | x, x, x ]
	                        *

右边的边界位置是 7 。两边界之差为 7 - 3 = 4，因此 3 在数组中出现了4次。

每个二分查找需要 4 步，因此一共需要 8 步。对于只有 12 个元素的数组效果不是太明显，但是数组越大优势越明显。如果是一个1,000,000 大小已排序的数组，也只需要 2 * 20 = 40 步就能统计某个数字出现的概率。

顺便起一下，如果你找的值不在数组中，`rightBoundary()` 和 `leftBoundary()`  会返回同样的值，因此他们之间的差为0。

这是一个通过修改二分查找来解决其他算法问题的例子。不过，它的前提是数组是已排序的。

*作者 Matthijs Hollemans  译者 KeithMorning*
