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

The idea behind the algorithm is not too different from the [naive string search](../Brute-Force%20String%20Search/) procedure. As it, Knuth-Morris-Pratt aligns the text with the pattern and goes with character comparisons from left to right. But, instead of making a shift of one character when a mismatch occurs, it uses a more intelligent way to move the pattern along the text. In fact, the algorithm features a pattern pre-processing stage where it acquires all the informations that will make the algorithm skip redundant comparisons, resulting in larger shifts.

The pre-processing stage produces an array (called `suffixPrefix` in the code) of integers in which every element `suffixPrefix[i]` records the length of the longest proper suffix of `P[0...i]` (where `P` is the pattern) that matches a prefix of `P`. In other words, `suffixPrefix[i]` is the longest proper substring of `P` that ends at position `i` and that is a prefix of `P`. Just a quick example. Consider `P = "abadfryaabsabadffg"`, then `suffixPrefix[4] = 0`, `suffixPrefix[9] = 2`, `suffixPrefix[14] = 4`.
There are different ways to obtain the values of `SuffixPrefix` array. We will use the method based on the [Z-Algorithm](../Z-Algorithm/). This function takes in input the pattern and produces an array of integers. Each element represents the length of the longest substring starting at position `i` of `P` and that matches a prefix of `P`. You can notice that the two arrays are similar, they record the same informations but on the different places. We only have to find a method to map `Z[i]` to `suffixPrefix[j]`. It is not that difficult and this is the code that will do for us:

预处理后得到一个整型数组（代码中命名为 `suffixPrefix`），数组每个元素 `suffixPrefix[i]` 记录的是 `P[0...i]` （ `P` 是模式串 ）最长的的后缀等于其前缀的长度。换句话说，`suffixPrefix[i]` 是 `P` 以 `i` 位置结束的最长子字符串就是 `P` 的一个前缀。（译者注：前缀指除了最后一个字符以外，一个字符串的全部头部组合；后缀指除了第一个字符以外，一个字符串的全部尾部组合。前缀和后缀的最长的共有元素的长度就是 `suffixPrefix` 要存的值）。比如 `P =  "abadfryaabsabadffg"`，则 `suffixPrefix[4] = 0`，`subffixPrefix[9] = 2`，`subffixPrefix[14] = 4`。（译者注：以 `suffixPrefix[9]` 为例，计算子字符串 `abadfryaab` , 其前缀集合为 `a, ab,aba,abad,abadf,abadfr,abadfry,abadfrya,abadfryaa` 和后缀集合为 `b,ab,aab,yaab,ryaab,fryaab,dfryaab,adfryaab,badfryaab`，相同的有 `ab,`因为匹配的只有一个，也就是最长值了，其长度为 2 ，因此 `subffixPrefix[9] = 2`。）计算这个并不复杂，可以使用如下的代码实现：

```swift
for patternIndex in (1 ..< patternLength).reversed() {
    textIndex = patternIndex + zeta![patternIndex] - 1
    suffixPrefix[textIndex] = zeta![patternIndex]
}
```

We are simply computing the index of the end of the substring starting at position `i` (as we know matches a prefix of `P`). The element of `suffixPrefix` at that index then it will be set with the length of the substring.

我们可以计算以 `i` 开始的子字符串，

Once the shift-array `suffixPrefix` is ready we can begin with pattern search stage. The algorithm first attempts to compare the characters of the text with those of the pattern. If it succeeds, it goes on until a mismatch occurs. When it happens, it checks if an occurrence of the pattern is present (and reports it). Otherwise, if no comparisons are made then the text cursor is moved forward, else the pattern is shifted to the right. The shift's amount is based on the `suffixPrefix` array, and it guarantees that the prefix `P[0...suffixPrefix[i]]` will match its opposing substring in the text. In this way, shifts of more than one character are often made and lot of comparisons can be avoided, saving a lot of time.

Here is the code of the Knuth-Morris-Pratt algorithm:

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

        /* Pre-processing stage: computing the table for the shifts (through Z-Algorithm) */
        let zeta = ZetaAlgorithm(ptnr: ptnr)

        for patternIndex in (1 ..< patternLength).reversed() {
            textIndex = patternIndex + zeta![patternIndex] - 1
            suffixPrefix[textIndex] = zeta![patternIndex]
        }

        /* Search stage: scanning the text for pattern matching */
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

Let's make an example reasoning with the code above. Let's consider the string `P = ACTGACTA"`, the consequentially obtained `suffixPrefix` array equal to `[0, 0, 0, 0, 0, 0, 3, 1]`, and the text `T = "GCACTGACTGACTGACTAG"`. The algorithm begins with the text and the pattern aligned like below. We have to compare `T[0]` with `P[0]`.  

                              1       
                    0123456789012345678
    text:           GCACTGACTGACTGACTAG
    textIndex:      ^
    pattern:        ACTGACTA
    patternIndex:   ^
                    x
    suffixPrefix:   00000031

We have a mismatch and we move on comparing `T[1]` and `P[0]`. We have to check if a pattern occurrence is present but there is not. So, we have to shift the pattern right and by doing so we have to check `suffixPrefix[1 - 1]`. Its value is `0` and we restart by comparing `T[1]` with `P[0]`. Again a mismath occurs, so we go on with `T[2]` and `P[0]`.

                              1      
                    0123456789012345678
    text:           GCACTGACTGACTGACTAG
    textIndex:        ^
    pattern:          ACTGACTA
    patternIndex:     ^
    suffixPrefix:     00000031

This time we have a match. And it continues until position `8`. Unfortunately the length of the match is not equal to the pattern length, we cannot report an occurrence. But we are still lucky because we can use the values computed in the `suffixPrefix` array now. In fact, the length of the match is `7`, and if we look at the element `suffixPrefix[7 - 1]` we discover that is `3`. This information tell us that that the prefix of `P` matches the suffix of the susbtring `T[0...8]`. So the `suffixPrefix` array guarantees us that the two substring match and that we do not have to compare their characters, so we can shift right the pattern for more than one character!
The comparisons restart from `T[9]` and `P[3]`.  

                              1       
                    0123456789012345678
    text:           GCACTGACTGACTGACTAG
    textIndex:               ^
    pattern:              ACTGACTA
    patternIndex:            ^
    suffixPrefix:         00000031

They match so we continue the compares until position `13` where a misatch occurs beetwen charcter `G` and `A`. Just like before, we are lucky and we can use the `suffixPrefix` array to shift right the pattern.

                              1       
                    0123456789012345678
    text:           GCACTGACTGACTGACTAG
    textIndex:                   ^
    pattern:                  ACTGACTA
    patternIndex:                ^
    suffixPrefix:             00000031

Again, we have to compare. But this time the comparisons finally take us to an occurrence, at position `17 - 7 = 10`.

                              1       
                    0123456789012345678
    text:           GCACTGACTGACTGACTAG
    textIndex:                       ^
    pattern:                  ACTGACTA
    patternIndex:                    ^
    suffixPrefix:             00000031

The algorithm than tries to compare `T[18]` with `P[1]` (because we used the element `suffixPrefix[8 - 1] = 1`) but it fails and at the next iteration it ends its work.


The pre-processing stage involves only the pattern. The running time of the Z-Algorithm is linear and takes `O(n)`, where `n` is the length of the pattern `P`. After that, the search stage does not "overshoot" the length of the text `T` (call it `m`). It can be be proved that number of comparisons of the search stage is bounded by `2 * m`. The final running time of the Knuth-Morris-Pratt algorithm is `O(n + m)`.


> **Note:** To execute the code in the [KnuthMorrisPratt.swift](./KnuthMorrisPratt.swift) you have to copy the [ZAlgorithm.swift](../Z-Algorithm/ZAlgorithm.swift) file contained in the [Z-Algorithm](../Z-Algorithm/) folder. The [KnuthMorrisPratt.playground](./KnuthMorrisPratt.playground) already includes the definition of the `Zeta` function.

Credits: This code is based on the handbook ["Algorithm on String, Trees and Sequences: Computer Science and Computational Biology"](https://books.google.it/books/about/Algorithms_on_Strings_Trees_and_Sequence.html?id=Ofw5w1yuD8kC&redir_esc=y) by Dan Gusfield, Cambridge University Press, 1997.

*Written for Swift Algorithm Club by Matteo Dunnhofer*
