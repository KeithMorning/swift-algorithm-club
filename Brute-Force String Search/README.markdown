# 字符串暴力搜索

如何不引用 Foundation 和 `NSString` 的 `rangeOfString()` 方法写一个纯 Swift 的字符串搜索算法？

目标：写一个 `String` 扩展方法 `indexOf(pattern: String)` 并返回第一个匹配字符串的 `String.Index` ，如何没有搜索到，返回 `nil` 。

例如：

```swift
// Input: 
let s = "Hello, World"
s.indexOf("World")

// Output:
<String.Index?> 7

// Input:
let animals = "🐶🐔🐷🐮🐱"
animals.indexOf("🐮")

// Output:
<String.Index?> 6
```

> **注意** ：这里🐮的索引值看起来好像是3，实际上为 6 。因为 emoji 需要更多的字符串表示。`String.Index` 的值不重要，只要它指向字符串中正确的字符。 

暴力搜索算法如下:

```swift
extension String {
  func indexOf(_ pattern: String) -> String.Index? {
    for i in self.characters.indices {
        var j = i
        var found = true
        for p in pattern.characters.indices{
            if j == self.characters.endIndex || self[j] != pattern[p] {
                found = false
                break
            } else {
                j = self.characters.index(after: j)
            }
        }
        if found {
            return i
        }
    }
    return nil
  }
}
```

这个算法遍历了原字符串中所有的字符。如果与搜索串的第一个字符相同，则内层循环对比搜索串中剩余的字符，如果没有匹配成功，外层从上次位置重新开始，直到找到一个匹配的字符串或者遍历结束。

暴力搜索算法可以实现，但是效率不高（也不优雅）。在短字符串上用用还行。如果需要一个又小在处理大量文本又高效的算法，可以试试 [Boyer-Moore](../Boyer-Moore/) 。

*作者 Matthijs Hollemans ，译者 KeithMorning*
