# Knuth-Morris-Pratt（KMP） 字符串搜索算法

目标：用 Swift 写一个线性的字符串搜索算法，返回模式串匹配到所有索引值。

换句话说就是，实现一个 `String` 的扩展方法 `indexesOf(Pattern:String)` ，函数返回 `[Int]` 表示模式串搜索到的所有索引值，如果没有匹配到，返回 `nil` 。

举例如下：

```swift
let dna = "ACCCGGTTTTAAAGAACCACCATAAGATATAGACAGATATAGGACAGATATAGAGACAAAACCCCATACCCCAATATTTTTTTGGGGAGAAAAACACCACAGATAGATACACAGACTACACGAGATACGACATACAGCAGCATAACGACAACAGCAGATAGACGATCATAACAGCAATCAGACCGAGCGCAGCAGCTTTTAAGCACCAGCCCCACAAAAAACGACAATFATCATCATATACAGACGACGACACGACATATCACACGACAGCATA"
dna.indexesOf(ptnr: "CATA")   // Output: [20, 64, 130, 140, 166, 234, 255, 270]

let concert = "🎼🎹🎹🎸🎸🎻🎻🎷🎺🎤👏👏👏"
concert.indexesOf(ptnr: "🎻🎷")   // Output: [6]
```

[Knuth-Morris-Pratt](https://en.wikipedia.org/wiki/Knuth–Morris–Pratt_algorithm) 算法被公认是字符串匹配查找的最好算法之一。虽然  [Boyer-Moore](../Boyer-Moore/) 更受欢迎，但是这个算法更加简单，也同样只需要线性的时间复杂度。

这个算法后的思想和原来的[暴力字符串搜索算法](../Brute-Force%20String%20Search/) 没什么不同，KMP 和它同样将字符串从左到右依次比较，但是与之不同的是不会在字符串不匹配时移动一个字符，而是用了更聪明的方式移动模式串。实际上这个算法对模式串特征做了预处理，使得它获得足够的信息能跳过不必要的比较，所以可以移动更多的距离。

预处理后得到一个整型数组（代码中命名为 `suffixPrefix`），数组每个元素 `suffixPrefix[i]` 记录的是 `P[0...i]` （ `P` 是模式串 ）最长的的后缀等于其前缀的长度。换句话说，`suffixPrefix[i]` 是 `P` 以 `i` 位置结束的最长子字符串就是 `P` 的一个前缀。（译者注：前缀指除了最后一个字符以外，一个字符串的全部头部组合；后缀指除了第一个字符以外，一个字符串的全部尾部组合。前缀和后缀的最长的共有元素的长度就是 `suffixPrefix` 要存的值）。比如 `P =  "abadfryaabsabadffg"`，则 `suffixPrefix[4] = 0`，`subffixPrefix[9] = 2`，`subffixPrefix[14] = 4`。（译者注：以 `suffixPrefix[9]` 为例，计算子字符串 `abadfryaab` , 其前缀集合为 `a, ab,aba,abad,abadf,abadfr,abadfry,abadfrya,abadfryaa` 和后缀集合为 `b,ab,aab,yaab,ryaab,fryaab,dfryaab,adfryaab,badfryaab`，相同的有 `ab`，因为匹配的只有一个，也就是最长值了，其长度为 2 ，因此 `subffixPrefix[9] = 2`。）计算这个并不复杂，可以使用如下的代码实现：

```swift
for patternIndex in (1 ..< patternLength).reversed() {
    textIndex = patternIndex + zeta![patternIndex] - 1
    suffixPrefix[textIndex] = zeta![patternIndex]
}
```

简单计算一下以索引值结束，以 `i` 开始的子字符串与 `P` 前缀是否匹配。把（匹配上的最长的）字符串长度赋值给`suffixPrefix` 数组的 Index 值 。

完成 `suffixPrefix` 偏移数组后，算法第一步就是尝试与模式串各个字符比较，如果比较成功，继续比较下一个，如果全部匹配，则直接移向下一段文本，否则需要将模式串右移，右移的位数根据 `suffixPrefix` ，它能够保证前缀 `P[0…suffixPrefix[i]]` 能够与对应的字符（后缀）相匹配（译者注：实际就是把后缀的位置替换为相同的前缀的位置）。通过这种方式可以大大减少匹配的次数，可以加快很多。

如下为 KMP 算法实现：

```swift
extension String {

    func indexesOf(ptnr: String) -> [Int]? {

        let text = Array(self.characters)
        let pattern = Array(ptnr.characters)

        let textLength: Int = text.count
        let patternLength: Int = pattern.count

        guard patternLength > 0 else {
            return nil
        }

        var suffixPrefix: [Int] = [Int](repeating: 0, count: patternLength)
        var textIndex: Int = 0
        var patternIndex: Int = 0
        var indexes: [Int] = [Int]()

        /* 预处理代码： 通过 Z-Algorithm 算法计算移动用的表*/
        let zeta = ZetaAlgorithm(ptnr: ptnr)

        for patternIndex in (1 ..< patternLength).reversed() {
            textIndex = patternIndex + zeta![patternIndex] - 1
            suffixPrefix[textIndex] = zeta![patternIndex]
        }

        /* 查询代码：查找模式串匹配值 */
        textIndex = 0
        patternIndex = 0

        while textIndex + (patternLength - patternIndex - 1) < textLength {

            while patternIndex < patternLength && text[textIndex] == pattern[patternIndex] {
                textIndex = textIndex + 1
                patternIndex = patternIndex + 1
            }

            if patternIndex == patternLength {
                indexes.append(textIndex - patternIndex)
            }

            if patternIndex == 0 {
                textIndex = textIndex + 1
            } else {
                patternIndex = suffixPrefix[patternIndex - 1]
            }
        }

        guard !indexes.isEmpty else {
            return nil
        }
        return indexes
    }
}
```

下面让我们解释一下上面的代码。如果 `P = "ACTGACTA"`，`suffixPrefix` 的结果为 `[0, 0, 0, 0, 0, 0, 3, 1]` ，文本为 `"GCACTGACTGACTGACTAG"`。算法开始的比较过程如下，先比较 `T[0]` 和 `P[0]`。

                              1       
                    0123456789012345678
    text:           GCACTGACTGACTGACTAG
    textIndex:      ^
    pattern:        ACTGACTA
    patternIndex:   ^
                    x
    suffixPrefix:   00000031

比较后发现不匹配，下一步比较 `T[1]` 和 `P[0]` ，不幸的是要检查模式串不一致，因此需要继续向右移动模式串，移动多少需要查询 `suffixPrefix[1 - 1]` 。如果值是 `0` ，需要再比较 `T[1]` 和 `P[0]` 。但还是不匹配，所以我们继续比较 `T[2]` 和 `P[0]`。 

                              1      
                    0123456789012345678
    text:           GCACTGACTGACTGACTAG
    textIndex:        ^
    pattern:          ACTGACTA
    patternIndex:     ^
    suffixPrefix:     00000031

这次有相同的字符了，但也是至相同到第 `8` 位置，不幸的是匹配的长度与模式串长度并不相同，因此不能认为是相同的，但还是有办法的，我们可以用 `suffixPrefix` 数组存的值，匹配的长度是 `7`， 查看 `suffixPrefix[7-1]` 的值是 `3`。这个信息告诉我们 `P` 的前缀与 `T[0...8]` 的子字符串是有匹配。`suffixPrefix` 数组保证我们模式串有两个子字符串是与之匹配的，因此不用再进行比较，我们可以直接大幅向右移动模式串！

从 `T[9]` 和 `P[3]` 重新比较。

                              1       
                    0123456789012345678
    text:           GCACTGACTGACTGACTAG
    textIndex:               ^
    pattern:              ACTGACTA
    patternIndex:            ^
    suffixPrefix:         00000031

继续比较直到第 13 位置，发现 `G` 和 `A` 不匹配。像上面那样，继续根据 `suffixPrefix` 数组进行右移。

                              1       
                    0123456789012345678
    text:           GCACTGACTGACTGACTAG
    textIndex:                   ^
    pattern:                  ACTGACTA
    patternIndex:                ^
    suffixPrefix:             00000031

再次进行比较，这次我们终于找到一个，从位置 `17 - 7 = 10`。

                              1       
                    0123456789012345678
    text:           GCACTGACTGACTGACTAG
    textIndex:                       ^
    pattern:                  ACTGACTA
    patternIndex:                    ^
    suffixPrefix:             00000031

算法再继续比较 `T[18]` 和 `P[1]`，（因为 suffixPrefix[8 - 1] = 1），但是并不相同，在下次循环后也就停止计算了。

预处理阶段只涉及到模式串，运行 Z-Algorithm 算法是线性的，只需要 `o(n)`，这里 `n` 是 `P` 的模式串长度。完成后，在查找阶段复杂度也不会超出文本 `T` 长度（设为 `m` ）。可以证明查找阶段的比较次数边界为 `2 * m`。所以 KMP 算法复杂度为 `O(n + m)`。


> 注意：如果你要执行 [KnuthMorrisPratt.swift](./KnuthMorrisPratt.swift) 需要拷贝  [Z-Algorithm](../Z-Algorithm/) 文件夹下的 [ZAlgorithm.swift](../Z-Algorithm/ZAlgorithm.swift)。 [KnuthMorrisPratt.playground](./KnuthMorrisPratt.playground)  已经包含 `Zeta` 函数。

声明：这段代码是基于 1997年 CUP Dan Gusfield 的[《Algorithm on String, Trees and Sequences: Computer Science and Computational Biology》](https://books.google.it/books/about/Algorithms_on_Strings_Trees_and_Sequence.html?id=Ofw5w1yuD8kC&redir_esc=y) 手册。

**作者 Matteo Dunnhofer，译者 KeithMorning**



**译者注**：由于本文原文在分析 KMP 算法上面明显不够用(虽然加了好多注释，😓)，关键的 `Next` 数组算法又没说明白，想继续挖坑的同学，推荐以下三篇文章，绝对够用。

* [唐小喵的解释](https://www.cnblogs.com/tangzhengyue/p/4315393.html)
* [孤~影的解释](http://www.cnblogs.com/yjiyjige/p/3263858.html)
* [阮老师的解说](http://www.ruanyifeng.com/blog/2013/05/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm.html)

