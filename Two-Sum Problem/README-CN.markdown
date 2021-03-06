# 两和问题

给定一个数字数组 `a`。写一个算法来检查数组中是否有两个项的和加起来等于一个给定的数字 `k`。换句话说，就是是否有 `a[i] + a[j] == k`？

还有一个这个问题的变体（有一些比另外一些好）。下面的解决方案的运行时间都是 **O(n)**。

# 方案 1

这个方案是用一个字典来存户数组中每个元素和要查找的 `k` 之间的差值。字典同时也保存每个元素的索引。

在这个方法里，字典中的每个键都对应一个新的目标值。如果数组中后面有哪个值和字典的键相等，那我们就知道存在两个数的和等于 `k`。

```swift
func twoSumProblem(_ a: [Int], k: Int) -> ((Int, Int))? {
  var dict = [Int: Int]()

  for i in 0 ..< a.count {
    if let newK = dict[a[i]] {
      return (newK, i)
    } else {
      dict[k - a[i]] =  i
    }
  }

  return nil  // if empty array or no entries sum to target k
}
```

`twoSumProblem()` 方法有两个参数：数字数组 `a` 和我们要找的和 `k`。返回找到的第一个索引集合 `(i, j)`，其中 `a[i] + a[j] == k`，或者是没有找到和为 `k` 的值时返回 `nil`。

让我们来看一个例子来看看这个算法是如何工作的。给定数组：

```swift
[ 7, 2, 23, 8, -1, 0, 11, 6  ]
```

我们来找找看是否存在两个数的和等于 10。

开始的时候，字典是空的。开始遍历每个元素：

- **i = 0**：7 是否在字典里？没有，将它和 `k` 值的差放到字典里。差是 `10 - 7 = 3`，所以字典的键是 `3`，键的值是当前的索引。字典现在是这样的：

```swift
[ 3: 0 ]
```

- **i = 1**：`2` 是否在字典里？没有，像上面一样将差值（`10 - 2 = 8`） 和数组的索引（`1`）放到字典里。字典现在是这样的：

```swift
[ 3: 0, 8: 1 ]
```

- **i = 2**： `23` 是否在字典里？没有。再一次将它放到字典里。差值是 `10 - 23 = -13`，索引是 `2`：

```swift
[ 3: 0, 8: 1, -13: 2 ]
```

- **i = 3**：`8` 是否在字典里？是的！这就意味着我们找到了一组值与我们的目标和是一致的。即 当前值 `8` 和 `array[dict[8]]`，因为 `dict[8] = 1`, `array[1] = 2`, 并且 `8 + 2 = 10`。因此，返回这些数字的索引。`8` 的索引就是当前循环的索引，`2` 的索引就是 `dict[8]` 或 `1`。返回的元组是 `(1, 3)`。

给定的数字其实还有很多别的答案：`(1, 3)` 和 `(4, 6)`。但是，只返回第一个。

这个算法的运行时间是 **O(n)**，因为有可能需要遍历整个数组。同样也需要 **O(n)** 的额外空间来存储字典。

# 方案 2

**注意**：这个特殊的算法要求数组是有序的，所以如果数组还没有排好序（通常来说是没有的），需要先排序。算法本身的复杂度是 **O(n)**，与前面的方法不同的是，它不需要额外的空间。当然，如果先要排序，总的时间复杂度就变成了 **O(n log n)**。有点遭但是还是可以接受的。

下面是 Swift 中的代码：

```swift
func twoSumProblem(_ a: [Int], k: Int) -> ((Int, Int))? {
  var i = 0
  var j = a.count - 1

  while i < j {
    let sum = a[i] + a[j]
    if sum == k {
      return (i, j)
    } else if sum < k {
      i += 1
    } else {
      j -= 1
    }
  }
  return nil
}
```

就像在第一个方案里一样， `twoSumProblem()` 函数需要两个参数：数字数组 `a` 和要找的和 `k`。如果有两个数的和是 `k`，函数就返回包含数组索引的元组。如果没有的话就返回 `nil`。主要的不同的是 `a` 默认是有序的。

要测试上面的代码就把它复制到 playground 里然后添加下面的代码：

```swift
let a = [2, 3, 4, 4, 7, 8, 9, 10, 12, 14, 21, 22, 100]
if let (i, j) = twoSumProblem(a, k: 33) {
  a[i] + a[j]  // 33
}
```

它会返回元组 `(8, 10)`，因为 `a[8] = 12` 和 `a[10] = 21` 并且他们的和是 `33`。

所以这个算法是如何工作的呢？它借助了数组是有序的这个优点。顺便说一下，很多算法都会这样做。如果先对数据进行排序，对于计算会简单很多。

在例子中，有序数组是：

	[ 2, 3, 4, 4, 7, 8, 9, 10, 12, 14, 21, 22, 100 ]

算法用两个变量 `i` 和 `j` 来分别指向数组的开头和结尾。然后增加 `i` 和减小 `j` 直到他们相遇。循环过程中，会检查 `a[i]` 和 `a[j]` 的和是否等于 `k`。

一步步来看看吧。开始的时候是这样的：

	[ 2, 3, 4, 4, 7, 8, 9, 10, 12, 14, 21, 22, 100 ]
      i                                        j

这两个数的和是 `2 + 100 = 102`。这显然太大了，因为这个例子中 `k = 33`。`100` 没有可能会是答案，所以 `j` 减 1.

现在有：

	[ 2, 3, 4, 4, 7, 8, 9, 10, 12, 14, 21, 22, 100 ]
      i                                    j

和是 `2 + 22 = 24` 。现在这个和有点小了。我们可以简单的得出 `2` 不可能会是答案。右边最大的数是 `22`，所以我们至少需要左边是 `11` 才能到 `33`.任何比 `11` 小的都可以直接跳过（这就是为什么要对数组排序的原因！）。

所以，`2` 就过去了，`i` 加 1 来看看下一个小数字。

	[ 2, 3, 4, 4, 7, 8, 9, 10, 12, 14, 21, 22, 100 ]
         i                                 j

和是 `3 + 22 = 25`。还是太小，`i` 再次加 1。

	[ 2, 3, 4, 4, 7, 8, 9, 10, 12, 14, 21, 22, 100 ]
            i                              j

实际上我们还要让 `i` 加好几次，直到我们到了 `12`：

	[ 2, 3, 4, 4, 7, 8, 9, 10, 12, 14, 21, 22, 100 ]
                               i           j

现在和是 `12 + 22 = 34`。有点大了，也就意味着我们要减 `j` 了。结果是：

	[ 2, 3, 4, 4, 7, 8, 9, 10, 12, 14, 21, 22, 100 ]
                               i       j

最后，终于找到了但按 `12 + 21 = 33`。耶！

当然可能不存在 `a[i] + a[j]` 的值等于 `k` 的情况。这时候，最终 `i` 和 `j` 指向了同一个数字。然后我们就可以得出没有答案的结论并且返回 `nil`。

我非常迷恋这个小算法。它展示了对于输入数据做一些基本的预处理 -- 从低到高排序 -- 就可以将一个复杂的问题变成一个非常简单和漂亮的算法。

*作者：Matthijs Hollemans 和 Daniel Speiser 翻译：Daisy*


