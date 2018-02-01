# 第K大元素

给定一个数组 `a` ，写一个算法找出第K大的元素。

比如在 *第一大* 的元素是最大元素。如果数组有 *n* 个元素，*第 n 大* 元素为最小值，中间最大为 *第n/2* 大值。

## 原始方案

下面的算法是半原生的。它的时间复杂度是 **O(n log n)**，因为它需要先排序，因此需要额外的 **O(n)** 的空间。

```swift
func kthLargest(a: [Int], k: Int) -> Int? {
  let len = a.count
  if k > 0 && k <= len {
    let sorted = a.sorted()
    return sorted[len - k]
  } else {
    return nil
  }
}
```

`kthLargest()` 函数有两个参数，整数型数组 `a` 和 `k` 用来表示第 *k* 大的元素。  

举例说明一下这个算法的原理，假定 `k = 4` ， 数组如下： 

```swift
[ 7, 92, 23, 9, -1, 0, 11, 6 ]
```

最开始无法直接找到第 k 大的元素，但是排序后就非常简单了，排序后如下：

```swift
[ -1, 0, 6, 7, 9, 11, 23, 92 ]
```

现在只需要取 `a.count - k` 对应的值：

```swift
a[a.count - k] = a[8 - 4] = a[4] = 9
```

当然如果需要找 *第 k 小* 的值时用 `a[k-1]` 即可

## 更快的算法

这个算法借鉴了 [二分查找](../Binary%20Search/) 和 [快排](../Quicksort/)  的思想，时间复杂度为 **O(n)**

不断调用二分查找将数组分割成一半又一半，快速的缩小查询的值的范围。

快速排序也分割数组，把小于轴值的移至左边，所有大于轴值的移至右边。经过某个轴值分区后，轴值所在的位置就是排序后最终位置。可以利用这一点来提高算法。

下面介绍如何工作：随机选一个值作为轴值进行分区，像二分查找一样继续对左右分区进行处理，直到恰好一个轴值是在 `k-th` 位置。

举个例子说明一下，在下面的数组中找 第 `4`  大的元素：

	[ 7, 92, 23, 9, -1, 0, 11, 6 ]
该算法对查找第k小值也是很简单的，来让我们试试查找`k = 4` 的最小值。 

我们不用先对数组排序，随机选一个值比如 `11` 作为轴值进行分区，结果如下：

	[ 7, 9, -1, 0, 6, 11, 92, 23 ]
	 <------ smaller    larger -->

根据结果，比 `11` 小的值在左边，大的值在右边。`11` 在它的最终位置上，索引值为 5 ， 因此第 4 小的值肯定是在左边的位置可以忽略其他的部分：

	[ 7, 9, -1, 0, 6, x, x, x ]
再随机选一个轴值比如 `6` 将数组分区，结果如下：

	[ -1, 0, 6, 9, 7, x, x, x ]
轴值 `6` 的索引值为 2，显然第 `4` 大的值在右边分区，可以忽略左边的分区了：

	[ x, x, x, 9, 7, x, x, x ]
重复以上操作后如下：

	[ x, x, x, 7, 9, x, x, x ]
轴值 `9` 的索引值为 4，而且这正是要查找的！可以看到我们不需要对数组排序，用很少的步数就能实现。

实现方法如下：

```swift
public func randomizedSelect<T: Comparable>(_ array: [T], order k: Int) -> T {
  var a = array

  func randomPivot<T: Comparable>(_ a: inout [T], _ low: Int, _ high: Int) -> T {
    let pivotIndex = random(min: low, max: high)
    a.swapAt(pivotIndex, high)
    return a[high]
  }

  func randomizedPartition<T: Comparable>(_ a: inout [T], _ low: Int, _ high: Int) -> Int {
    let pivot = randomPivot(&a, low, high)
    var i = low
    for j in low..<high {
      if a[j] <= pivot {
        a.swapAt(i, j)
        i += 1
      }
    }
    a.swapAt(i, high)
    return i
  }

  func randomizedSelect<T: Comparable>(_ a: inout [T], _ low: Int, _ high: Int, _ k: Int) -> T {
    if low < high {
      let p = randomizedPartition(&a, low, high)
      if k == p {
        return a[p]
      } else if k < p {
        return randomizedSelect(&a, low, p - 1, k)
      } else {
        return randomizedSelect(&a, p + 1, high, k)
      }
    } else {
      return a[low]
    }
  }

  precondition(a.count > 0)
  return randomizedSelect(&a, 0, a.count - 1, k)
}
```

为了提高可读性，这个函数分成三个内部函数：

- `randomPivot()` 随机选取一个数字，然后放在当前分区的最后一个位置（这是Lomuto 分区方式所规定的，更多介绍请看[快排](../Quicksort/)）
- `randomizedPartition()` 是快排中 Lomuto 分区方法。当完成后，随机轴值在的位置就是排序后的最终位置。返回轴值所在的位置。
- `randomizedSelect()` 做所有的脏活累活。先调用分区函数，后决定再做什么。如果轴值索引值等于 k ,那么该值正是查找值，完成查找。如果 `k` 比该索引值小，那么查找值一定在左边分区，递归调用就可以了，否则就肯定是在右边分区中。

非常😎，是不是？ 快排的期望复杂度为 **o(n log n)**， 但是因为只把数组分成越来越小的分区，`randomizedSelect()` 的时间复杂度为 **O(n)**。

> 注意：该函数式计算数组中 *第k* 小元素，`k` 是从 0 开始的。如果需要 `第k` 大元素，应调用 `a.count - k`。

*作者 Daniel Speiser 修改 Matthijs Hollemans 译者 KeithMorning*

