# 二维数组

在 C 和 Objective-C 中你可以写出如下代码代表一个 9x7 格子的点心。

	int cookies[9][7];

这是一个包含 64 个元素的二维数组。查找第 3 列第 6 行的点心代码如下：



	myCookie = cookies[3][6];

但是无法在 Swift 中这么写， 要想创建一个多维数组可以这么写：

```swift
var cookies = [[Int]]()
for _ in 1...9 {
  var row = [Int]()
  for _ in 1...7 {
    row.append(0)
  }
  cookies.append(row)
}
```

定位到某个点心代码：

```swift
let myCookie = cookies[3][6]
```

也可以一行代码搞定：

```swift
var cookies = [[Int]](repeating: [Int](repeating: 0, count: 7), count: 9)
```

上面代码有点复杂，可以定义一个辅助函数：

```swift
func dim<T>(_ count: Int, _ value: T) -> [T] {
  return [T](repeating: value, count: count)
}
```

创建数组如下：

```swift
var cookies = dim(9, dim(7, 0))
```

因为默认值是 `0`, Swift 会推断数组的类型为 `Int` 。如果用 string 类型，可以这么写：

```swift
var cookies = dim(9, dim(7, "yum"))
```

`dim()` 函数可以很轻松的扩展到更多维：

```swift
var threeDimensions = dim(2, dim(3, dim(4, 0)))
```

用这种方式表示多维数组或多重数组难以看出表示的数组维度。

用下面的代码可以更加容易的创建自定义类型的 2-D 数组：

```swift
public struct Array2D<T> {
  public let columns: Int
  public let rows: Int
  fileprivate var array: [T]
  
  public init(columns: Int, rows: Int, initialValue: T) {
    self.columns = columns
    self.rows = rows
    array = .init(repeating: initialValue, count: rows*columns)
  }
  
  public subscript(column: Int, row: Int) -> T {
    get {
      precondition(column < columns, "Column \(column) Index is out of range. Array<T>(columns: \(columns), rows:\(rows))")
      precondition(row < rows, "Row \(row) Index is out of range. Array<T>(columns: \(columns), rows:\(rows))")
      return array[row*columns + column]
    }
    set {
      precondition(column < columns, "Column \(column) Index is out of range. Array<T>(columns: \(columns), rows:\(rows))")
      precondition(row < rows, "Row \(row) Index is out of range. Array<T>(columns: \(columns), rows:\(rows))")
      array[row*columns + column] = newValue
    }
  }
}
```

`Array2D` 使用了泛型，因此可以包含任意的对象类，不再单单是数字。

用如下的方式创建 `Array2D` 实例：

```swift
var cookies = Array2D(columns: 9, rows: 7, initialValue: 0)
```

通过 `subscript` 函数可以从数组中取对象：

```swift
let myCookie = cookies[column, row]
```

或者修改数组内容：

```swift
cookies[column, row] = newCookie
```

实际上 `Array2D` 内部用了一个一维的数组来保存数据。 数据在数组中的位置用 `(row x numberOfColumns) + column` 来表示，但是 `Array2D` 的使用者只关心 “column” 和 “row”，所有的工作由 `Array2D` 来实现。 这就是类或结构体封装原始类型的优点。

**作者 Matthijs Hollemans，译者 KeithMorning**