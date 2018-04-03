# Rabin-Karp 字符串搜索算法

Rabin-Karp 字符串搜索算法用用来搜索文本中的模式串。

该算法的典型应用就是检测抄袭。给一篇原文，它可以忽略细节和标点，非常快的从一篇论文中找到原文中的的句子。对于大规模文本查找，逐字查找是不靠谱的。

## 举个例子

如果文本为 "The big dog jumped over the fox" ，模式串为 "ump"，查询的结果为 13。

先对 "ump" 做哈希，然后再对 "The" 计算哈希值。如果不匹配，向前一次移动一个字符，比如这次为 "he "(注意这里有原文的空格)，并从前一次中减去 "T" 哈希值部分。

## 算法实现

Rabin-Karp 算法移动的步进为模式串的长度。它先对模式串取哈希，然后对文本前 x 个字符取哈希，这里的 x 为模式串的长度。它每次向前移动一个字符，并用之前的哈希值计算新的哈希值会使得更快。直到找到与模式串相同的哈希值，在比较两个字符是否是一样（为了避免哈希碰撞造成假象）。

## 代码

主代码如下，详细的实现请查看 rabin-karp.swift

```swift
public func search(text: String , pattern: String) -> Int {
    // 将数组转为整形数组
    let patternArray = pattern.flatMap { $0.asInt }
    let textArray = text.flatMap { $0.asInt }

    if textArray.count < patternArray.count {
        return -1
    }

    let patternHash = hash(array: patternArray)
    var endIdx = patternArray.count - 1
    let firstChars = Array(textArray[0...endIdx])
    let firstHash = hash(array: firstChars)

    if (patternHash == firstHash) {
        // 确认一下不是哈希冲突
        if firstChars == patternArray {
            return 0
        }
    }

    var prevHash = firstHash
    // 在被查询的文本中滑动
    for idx in 1...(textArray.count - patternArray.count) {
        endIdx = idx + (patternArray.count - 1)
        let window = Array(textArray[idx...endIdx])
        let windowHash = nextHash(prevHash: prevHash, dropped: textArray[idx - 1], added: textArray[endIdx], patternSize: patternArray.count - 1)

        if windowHash == patternHash {
            if patternArray == window {
                return idx
            }
        }

        prevHash = windowHash
    }

    return -1
}
```

在 playground 测试一下:

```swift
  search(text: "The big dog jumped", "ump")
```

结果返回 13，因为字符串从 0 开始计数，所以 ump 是在第 13 位置。

## 更多参考

[Rabin-Karp Wikipedia](https://en.wikipedia.org/wiki/Rabin%E2%80%93Karp_algorithm)


**作者 [Bill Barbour](https://github.com/brbatwork)， 译者 [KeithMorning](https://github.com/KeithMorning/)**
