# Z-Algorithm 字符串搜索

目标：给定一个模式串，用 Swift 写一个线性时间复杂度的匹配算法，返回在字符串中出现的位置。

换而言之，我们需要实现一个 `String` 的扩展方法 `indexsOf(pattern:String)`, 能够返回一个 `[Int]` 数组代表所有模式串出现的索引位置，或者返回 `nil` 如果没有在字符串中找到。

举个例子:

```swift
let str = "Hello, playground!"
str.indexesOf(pattern: "ground")   // Output: [11]

let traffic = "🚗🚙🚌🚕🚑🚐🚗🚒🚚🚎🚛🚐🏎🚜🚗🏍🚒🚲🚕🚓🚌🚑"
traffic.indexesOf(pattern: "🚑") // Output: [4, 21]
```

许多字符串搜索算法都会有一个预处理函数计算一个表用在随后的计算过程中。这个表可以可以节省模式串匹配阶段的一些时间，因为可以避免一些不必要的字符串比较。Z-Algorithm 就是众多预处理函数的一种。尽管它生为预处理函数（在 [KMP](../Knuth-Morris-Pratt/) 算法和其他算法中就承担了一个这样的角色），但是本文将介绍如何将它作为字符串搜索算法使用。

### Z-Algorithm 模式串的前缀

正如本文所说，Z-Algorithm 是算法开头用来处理模式串的用来计算出一个跳过非必要比较的表。Z-Algorithm 计算模式串后得到一个整数数组（文献中称之为 `Z`）每个元素称作 `Z[i]`, 表示 `P` 的以 `i` 开始的最长子字符串的前缀与 `P` 的前缀相匹配的长度。简而言之就是 `Z[i]` 记录了 `P[i...|P|]` 最长的与 `P` 前缀相同的前缀。举个例子，假设 `P = "ffgtrhghhffgtggfredg"`。那么 `z[5] =0 (f...h...)`，`z[9] = 4 (ffgtr...ffgtg...)` 和 `z[15] = 1 (ff..fr..)`。（译者注：好吧，这个例子其实很难看，相信你可能数的眼都花了，这里 `z[5] = hghhffgtggfredg` 与原字符串比较前缀一个都没有所以结果为0，而 `z[9] = ffgtggfredg` 与原字符串比较一下结果为 `ffgt` 相同，结果为 4。）

但是我们如何计算 `Z `? 在介绍这个算法之前，我们需要先介绍一个下 Z-box 这个概念。 一个 Z-Box  含有 `(left,right)` 一对值，用来在计算过程中记录最长的与 `P` 前缀相同的子字符串。`left` 和 `right` 这两个索引值各自代表该子字符串的左边界和右边界索引。Z-Algorithm 定义比较感性，它从 `k-1` 开始，计算了模式串中每个位置 `k`。算法背后的思想是之前计算的值可以通过避免重复已经比较过的值加快 `Z[k + 1]` 的演算。思考一下：如果迭代到 `k = 100`, 分析模式串 `100` 位置如何计算。所有的 `Z[1]` 到 `Z[99]` 已经计算过且 `left = 70`, `right = 120`。这意味着子字符串长度为 `51` ，从 `70` 开始到 `120`结束，而且还与模式串前缀相匹配的。推理一下后可以发现从 `100` 开始，长度为 `21` 的字符与模式串中从 `30` 开始长度为 `21` 的子字符串相匹配（因为我们是在一个与模式串前缀相匹配的子字符串中）。因此我们可以避免额外的比较直接用 `Z[30]` 来计算 `Z[100]`。

这是这个算法背后的简单思想。无法直接通过之前结果计算的情况很少，但还是有一些比较需要单独处理一下。

下面是计算 `Z-array` 的代码：

```swift
func ZetaAlgorithm(ptrn: String) -> [Int]? {

    let pattern = Array(ptrn.characters)
    let patternLength: Int = pattern.count

    guard patternLength > 0 else {
        return nil
    }

    var zeta: [Int] = [Int](repeating: 0, count: patternLength)

    var left: Int = 0
    var right: Int = 0
    var k_1: Int = 0
    var betaLength: Int = 0
    var textIndex: Int = 0
    var patternIndex: Int = 0

    for k in 1 ..< patternLength {
        if k > right {  // Outside a Z-box: compare the characters until mismatch
            patternIndex = 0

            while k + patternIndex < patternLength  &&
                pattern[k + patternIndex] == pattern[patternIndex] {
                patternIndex = patternIndex + 1
            }

            zeta[k] = patternIndex

            if zeta[k] > 0 {
                left = k
                right = k + zeta[k] - 1
            }
        } else {  // Inside a Z-box
            k_1 = k - left + 1
            betaLength = right - k + 1

            if zeta[k_1 - 1] < betaLength { // Entirely inside a Z-box: we can use the values computed before
                zeta[k] = zeta[k_1 - 1]
            } else if zeta[k_1 - 1] >= betaLength { // Not entirely inside a Z-box: we must proceed with comparisons too
                textIndex = betaLength
                patternIndex = right + 1

                while patternIndex < patternLength && pattern[textIndex] == pattern[patternIndex] {
                    textIndex = textIndex + 1
                    patternIndex = patternIndex + 1
                }

                zeta[k] = patternIndex - k
                left = k
                right = patternIndex - 1
            }
        }
    }
    return zeta
}
```

Let's make an example reasoning with the code above. Let's consider the string `P = “abababbb"`. The algorithm begins with `k = 1`, `left = right = 0`. So, no Z-box is "active" and thus, because `k > right` we start with the character comparisons beetwen `P[1]` and `P[0]`.


       01234567
    k:  x
       abababbb
       x
    Z: 00000000
    left:  0
    right: 0

We have a mismatch at the first comparison and so the substring starting at `P[1]` does not match a prefix of `P`. So, we put `Z[1] = 0` and let `left` and `right` untouched. We begin another iteration with `k = 2`, we have `2 > 0` and again we start comparing characters `P[2]` with `P[0]`. This time the characters match and so we continue the comparisons until a mismatch occurs. It happens at position `6`. The characters matched are `4`, so we put `Z[2] = 4` and set `left = k = 2` and `right = k + Z[k] - 1 = 5`. We have our first Z-box that is the substring `"abab"` (notice that it matches a prefix of `P`) starting at position `left = 2`.

       01234567
    k:   x
       abababbb
       x
    Z: 00400000
    left:  2
    right: 5

We then proceed with `k = 3`. We have `3 <= 5`. We are inside the Z-box previously found and inside a prefix of `P`. So we can look for a position that has a previously computed value. We calculate `k_1 = k - left = 1` that is the index of the prefix's character equal to `P[k]`. We check `Z[1] = 0` and `0 < (right - k + 1 = 3)` and we find that we are exactly inside the Z-box. We can use the previously computed value, so we put `Z[3] = Z[1] = 0`, `left` and `right` remain unchanged.
At iteration `k = 4` we initially execute the `else` branch of the outer `if`. Then in the inner `if` we have that `k_1 = 2` and `(Z[2] = 4) >= 5 - 4 + 1`. So, the substring `P[k...r]` matches for `right - k + 1 = 2` chars the prefix of `P` but it could not for the following characters. We must then compare the characters starting at `r + 1 = 6` with those starting at `right - k + 1 = 2`. We have `P[6] != P[2]` and so we have to set `Z[k] = 6 - 4 = 2`, `left = 4` and `right = 5`.

       01234567
    k:     x
       abababbb
       x
    Z: 00402000
    left:  4
    right: 5

With iteration `k = 5` we have `k <= right` and then `(Z[k_1] = 0) < (right - k + 1 = 1)` and so we set `z[k] = 0`. In iteration `6` and `7` we execute the first branch of the outer `if` but we only have mismatches, so the algorithms terminates returning the Z-array as `Z = [0, 0, 4, 0, 2, 0, 0, 0]`.

The Z-Algorithm runs in linear time. More specifically, the Z-Algorithm for a string `P` of size `n` has a running time of `O(n)`.

The implementation of Z-Algorithm as string pre-processor is contained in the [ZAlgorithm.swift](./ZAlgorithm.swift) file.

### Z-Algorithm as string search algorithm 

The Z-Algorithm discussed above leads to the simplest linear-time string matching algorithm. To obtain it, we have to simply concatenate the pattern `P` and text `T` in a string `S = P$T` where `$` is a character that does not appear neither in `P` nor `T`. Then we run the algorithm on `S` obtaining the Z-array. All we have to do now is scan the Z-array looking for elements equal to `n` (which is the pattern length). When we find such value we can report an occurrence.

```swift
extension String {

    func indexesOf(pattern: String) -> [Int]? {
        let patternLength: Int = pattern.characters.count
        /* Let's calculate the Z-Algorithm on the concatenation of pattern and text */
        let zeta = ZetaAlgorithm(ptrn: pattern + "💲" + self)

        guard zeta != nil else {
            return nil
        }

        var indexes: [Int] = [Int]()

        /* Scan the zeta array to find matched patterns */
        for i in 0 ..< zeta!.count {
            if zeta![i] == patternLength {
                indexes.append(i - patternLength - 1)
            }
        }

        guard !indexes.isEmpty else {
            return nil
        }

        return indexes
    }
}
```

Let's make an example. Let `P = “CATA“` and `T = "GAGAACATACATGACCAT"` be the pattern and the text. Let's concatenate them with the character `$`. We have the string `S = "CATA$GAGAACATACATGACCAT"`. After computing the Z-Algorithm on `S` we obtain:

                1         2
      01234567890123456789012
      CATA$GAGAACATACATGACCAT
    Z 00000000004000300001300
                ^

We scan the Z-array and at position `10` we find `Z[10] = 4 = n`. So we can report a match occuring at text position `10 - n - 1 = 5`.

As said before, the complexity of this algorithm is linear. Defining `n` and `m` as pattern and text lengths, the final complexity we obtain is `O(n + m + 1) = O(n + m)`.


Credits: This code is based on the handbook ["Algorithm on String, Trees and Sequences: Computer Science and Computational Biology"](https://books.google.it/books/about/Algorithms_on_Strings_Trees_and_Sequence.html?id=Ofw5w1yuD8kC&redir_esc=y) by Dan Gusfield, Cambridge University Press, 1997. 

*Written for Swift Algorithm Club by Matteo Dunnhofer*
