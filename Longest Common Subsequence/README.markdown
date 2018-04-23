# 最长公共子序列算法

两个字符串的最长公共子序列（LCS）是指这两个字符串中最长的有相同顺序的子序列。

举例说明一下，`"Hello World"` 和 `"Bonjour le monde"` 的 LCS 是 `"oorld"`。如果从左到右依次扫过字符串，你会发现 `o`、 `o`、 `r` 、`l`、 `d`  在两个字符串中出现的顺序是一样的。

其他的子序列为 `"ed"` 和 `"old"`，但是它们都比 `"oorld"` 要短。

> 注意：不要和最长公共字符串混淆了，后者必须是两个字符串的子字符串，也就是字符是直接相邻的。但对公共序列来说，字符之间并不是连续，但是它们必须有相同的顺序。

计算两个字符串 `a` 和 `b` 的 LCS 方法之一是通过动态规划和回溯法。



## 通过动态规划计算 LCS 的长度

首先，我们需要计算 `a`  和 `b` 最长的公共子序列，先不需要查找确切的子序列，只是确定长度是多少。

为了计算 `a` 和 `b` 所有子混合字符串 LCS 的长度，我们可以使用 **动态规划**技术。动态规划基本方法是计算出所有的可能并存入一个待查询的表中。

> 注意：在下面介绍中， `n` 是 `a` 字符的长度, `m` 是 `b` 的字符长度。

为了找出所有可能的子序列，先写一个帮助函数 `lcsLength(_:)`。这个函数会创建一个 `(n+1)` * `(m+1)` 的矩阵，这里 `matrix[x][y]` 是 字符串 `a[0...x-1]` 和 `b[0...y-1]` 的 LCS 长度。

比如字符串如下 `"ABCBX"` 和 `"ABDCAB"` ，函数 `lcsLength(_:)` 矩阵输出如下：

```
|   | Ø | A | B | D | C | A | B |
| Ø | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| A | 0 | 1 | 1 | 1 | 1 | 1 | 1 |  
| B | 0 | 1 | 2 | 2 | 2 | 2 | 2 |
| C | 0 | 1 | 2 | 2 | 3 | 3 | 3 |
| B | 0 | 1 | 2 | 2 | 3 | 3 | 4 |
| X | 0 | 1 | 2 | 2 | 3 | 3 | 4 |
```

在这个例子中，查看 `matrix[3][4]` 的值为 `3`。 这意味着子字符串 `a[0...2]` 和 `b[0...3]` 或者说 `"ABC"` 和`"ABDC"` LCS 的长度是 3。 这个值确实正确，因为两字符串有相同的子序列 `ABC`。（注意：第一行列的矩阵值用 0 填充。）

`lcsLength(_:)` 的源码如下，这段代码在 `String` 扩展中：

```swift
func lcsLength(_ other: String) -> [[Int]] {

  var matrix = [[Int]](repeating: [Int](repeating: 0, count: other.characters.count+1), count: self.characters.count+1)

  for (i, selfChar) in self.characters.enumerated() {
	for (j, otherChar) in other.characters.enumerated() {
	  if otherChar == selfChar {
        // 找到公共字符，当前 lcs 的最大长度加 1。
		matrix[i+1][j+1] = matrix[i][j] + 1
	  } else {
        // 没有找到匹配的，接着使用当前最大的 lcs 长度
		matrix[i+1][j+1] = max(matrix[i][j+1], matrix[i+1][j])
	  }
	}
  }

  return matrix
}
```

首先，创建一个新的矩阵 —— 二维数组 —— 全部用零填充。然后在 `self` 和 `other` 两个字符串中查找，比较它们的字符串后按顺序填充到矩阵中。如果两个字符相同，增大序列的长度。如果两个字符不同，“复制” 当前最大 LCS。

比如如下的情况：

```
|   | Ø | A | B | D | C | A | B |
| Ø | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| A | 0 | 1 | 1 | 1 | 1 | 1 | 1 |  
| B | 0 | 1 | 2 | 2 | 2 | 2 | 2 |
| C | 0 | 1 | 2 | * |   |   |   |
| B | 0 |   |   |   |   |   |   |
| X | 0 |   |   |   |   |   |   |
```

`*` 表示我们当前的比较的两个字符 `C` 和 `D` 。这两个字符不相同，因此用之前找到的最大长度 `2` 作为结果：

```
|   | Ø | A | B | D | C | A | B |
| Ø | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| A | 0 | 1 | 1 | 1 | 1 | 1 | 1 |  
| B | 0 | 1 | 2 | 2 | 2 | 2 | 2 |
| C | 0 | 1 | 2 | 2 | * |   |   |
| B | 0 |   |   |   |   |   |   |
| X | 0 |   |   |   |   |   |   |
```

现在比较 `C` 和 `C`。 他们两个相同，因此增加长度到 `3`：

```
|   | Ø | A | B | D | C | A | B |
| Ø | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| A | 0 | 1 | 1 | 1 | 1 | 1 | 1 |  
| B | 0 | 1 | 2 | 2 | 2 | 2 | 2 |
| C | 0 | 1 | 2 | 2 | 3 | * |   |
| B | 0 |   |   |   |   |   |   |
| X | 0 |   |   |   |   |   |   |
```

一次类推，这就是 `lcsLength(_:)` 如何计算填充整个矩阵的过程。

## 回溯查找出序列

目前我们计算出每个可能的序列的长度。最长的序列可以再矩阵的右下角，位置为 `matrix[n+1][m+1]`。在上面的例子值为 4 ，因此 LCS 包含 4 个字符。 

计算出全部公共子序列的长度后就可以通过回溯法计算出**那些**字符是 LCS 的组成部分。

回溯法从 `matrix[n+1][m+1]` 开始，向左边和上边(以此为优先级)方向搜寻没有简单传播的变化。

```
|   |  Ø|  A|  B|  D|  C|  A|  B|
| Ø |  0|  0|  0|  0|  0|  0|  0|
| A |  0|↖ 1|  1|  1|  1|  1|  1|  
| B |  0|  1|↖ 2|← 2|  2|  2|  2|
| C |  0|  1|  2|  2|↖ 3|← 3|  3|
| B |  0|  1|  2|  2|  3|  3|↖ 4|
| X |  0|  1|  2|  2|  3|  3|↑ 4|
```

每一个 `↖` 代表一个属于 LCS 的字符（在行/列的头部）。

如果左边和上边的数字与当前单元的数字不同，那就没有产生传播。这样的情况下 `matrix[i][j]` 代表字符串 `a` 和 `b` 的一个公共字符，因此 `a[i-1]` 和 `b[j-1]` 就是正在寻找的 LCS 的组成部分。

需要注意的是，因为是反向运行的， LCS 是倒序组成的，在返回结果之前，需要把结果做反向排序后才是正确的 LCS。

下面是回溯的代码：

```swift
func backtrack(_ matrix: [[Int]]) -> String {
  var i = self.characters.count
  var j = other.characters.count
  
  var charInSequence = self.endIndex
  
  var lcs = String()
  
  while i >= 1 && j >= 1 {
    // 表示传播没有变化：没有新字符添加到 lcs
	if matrix[i][j] == matrix[i][j - 1] {
	  j -= 1
	}
    // 表示传播没有变化：没有新字符添加到 lcs
	else if matrix[i][j] == matrix[i - 1][j] {
	  i -= 1
	  charInSequence = self.index(before: charInSequence)
	}
    // 左边和上面的字符与当前单元均不相同
    // 意味着 lcs 长度加1
	else {
	  i -= 1
	  j -= 1
	  charInSequence = self.index(before: charInSequence)
	  lcs.append(self[charInSequence])
	}
  }
  
  return String(lcs.characters.reversed())
}
```

回溯法从 `matrix[n+1][m+1]`（右下角）到 `matrix[1][1]` （左上角），查找两个字符的公共字符串，添加这些字符到新字符串 `lcs` 中。

`charInSequence`  变量是 `self` 字符串的索引。开始时指向字符串的最后一个位置。每次我们减小 `i` ，也会将 `charInSequence` 回退。当两个字符相同时，将处于 `self[charInSequence]` 的字符添加到新 `lcs` 字符串中。（不能直接写 `self[i]` 因为 `i` 可能在 Swift 字符串中并不指向此位置。）

由于回溯法是倒序添加字符，所以在函数最后调用 `reversed()` 把字符串调整成正确的顺序。（每次添加到字符尾部然后一次性反过来要比每次把字符插到字符串的前面要快。）

## 整合一下

先调用 `lcsLength(_:)` 找到两个字符串的 LCS，然后再调用 `backtrack(_:)`：

```swift
extension String {
  public func longestCommonSubsequence(_ other: String) -> String {

    func lcsLength(_ other: String) -> [[Int]] {
      ...
    }
    
    func backtrack(_ matrix: [[Int]]) -> String {
      ...
    }

    return backtrack(lcsLength(other))
  }
}
```

为了保持代码整洁，两个帮助函数在主函数 `longestCommonSubsequence()` 中折叠起来了。

在 Playground 中试试下面代码：

```swift
let a = "ABCBX"
let b = "ABDCAB"
a.longestCommonSubsequence(b)   // "ABCB"

let c = "KLMK"
a.longestCommonSubsequence(c)   // "" (no common subsequence)

"Hello World".longestCommonSubsequence("Bonjour le monde")   // "oorld"
```

**作者 Pedro Vereza， 译者 KeithMorning**
