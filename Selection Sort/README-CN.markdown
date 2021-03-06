# 选择排序

目标：从低到过（或从高到低）对数组进行排序。

给定一个包含数字的数组，需要将他们放在正确的顺序上。选择排序算法将数组分成两部分：数组的开始是有序的，剩下的数组就是由还没有排序的数字组成。

	[ ...sorted numbers... | ...unsorted numbers... ]

这和 [插入排序](../Insertion%20Sort/README-CN.markdown) 类似，不同的是新数字添加到已排序部分的方法。

步骤如下：

- 找到数组中最小的数。从索引 0 开始，遍历整个数组，记录最小的数字。
- 将最小的数与索引 0 的数字交换。现在排好序的部分就是由索引 0 所在的数字组成。
- 然后轮到索引 1 了。
- 在剩下的数组中找到最小的值。这次从索引 1 开始。这也需要遍历整个数组然后记录遇到的最小值。
- 将它与索引 1 的数字进行交换。现在已排序部分就由两个数字组成了，范围是从索引 0 到 1。
- 现在是索引 2 了。
- 找到剩下的数组中的最小值，从索引 2 开始，然后将它与索引 2 的元素进行交换。现在数组的索引 0 到 2 都是有序的；这里包含数组中最小的三个数字。
- 一直这样进行下去，直到剩下的数字都排好序。

这就叫做 “选择” 排序，因为每一步都是从剩下的数组中选择一个最小的数字。

## 例子

假设要排序的数字是 `[ 5, 8, 3, 4, 6 ]`。同时我们也要追踪有序数组是在哪里结束的，由 `|` 符号分隔。

开始的时候，有序数组是空的：

	[| 5, 8, 3, 4, 6 ]

现在我们找数组中最小的数字。通过从左到右遍历整个数组来找，从 `|` 开始，找到数字 `3`。

为了将数字放到有序的位置，将它与挨着 `|` 符号的数字，也就是 `5` 进行交换：

	[ 3 | 8, 5, 4, 6 ]
	  *      *

有序的部分现在是 `[ 3 ]`，剩下的是 `[ 8, 5, 4, 6 ]`。

再一次，继续找最小的数字，从 `|` 开始。找到的是 `4` ，然后将它和 `8` 进行交换：

	[ 3, 4 | 5, 8, 6 ]
	     *      *

每一步的时候， `|` 都是向右移动一个位置。再从剩下的数组中找到最小的数字 `5` 。这回没必要进行交换了，因为 `5` 就在它所在的位置上，我们直接往前移动即可：

	[ 3, 4, 5 | 8, 6 ]
	        *

重复这个过程直到数组已经排好序。注意到，`|` 左边都是排好续的，并且是包含了数组中最小的元素。最后，排好序的结果是：

	[ 3, 4, 5, 6, 8 |]

正如你看到的，选择排序是一个 *原地* 排序，因为所有的都是发生在原来的数组里；不需要额外的内存。也可以将它实现成一个 *稳定* 排序，这样所有相同的元素就不用进行交换了，而保持它们之前的相对位置（下面给出的版本不是稳定的）。

## 代码

下面是 Swift 中选择排序的实现：

```swift
func selectionSort(_ array: [Int]) -> [Int] {
  guard array.count > 1 else { return array }  // 1

  var a = array                    // 2

  for x in 0 ..< a.count - 1 {     // 3
    
    var lowest = x
    for y in x + 1 ..< a.count {   // 4
      if a[y] < a[lowest] {
        lowest = y
      }
    }
    
    if x != lowest {               // 5
      swap(&a[x], &a[lowest])
    }
  }
  return a
}
```

将代码放在 playground 中，然后像这样来测试：

```swift
let list = [ 10, -1, 3, 9, 2, 27, 8, 5, 1, 3, 0, 26 ]
selectionSort(list)
```

代码步骤解析：

1. 如果数组是空的或者只包含一个元素，那么就没有必要进行排序了。

2. 复制一份数组。因为在 Swift 中我们不能直接修改 `array` 参数的内容。就像 Swift 自己的 sort() 一样，`selectionSort()` 函数也会返回一份原始数组的 *拷贝*。
3. 在函数中有两个循环。外层的循环按顺序查看数组的每个元素；也就是将 `|` 符号往前挪动。
4. 这是内层循环。用来找到剩下数组中最小的数字。
5. 将最小的数字和当前的索引的元素进行交换。`if` 用来检查是否要进行交换，因为在 Swift 中不能跟自己 `swap()` 。

总结：对于数组中的每个元素，选择排序将剩下数组中的最小值与当前的位置进行交换。结果是，数组从左到右变成有序的了。（也可以从右到左进行，这样的话，要找的就是最大的值了。试试吧！）

> **注意：** 外层循环是在索引 a.count - 2 的位置结束的。最后一个元素会自动在正确的位置，因为没有要进行排序的元素了。

[SelectionSort.swift](SelectionSort.swift) 的源代码中有一个用泛型实现的版本，可以用它来堆字符串或者其他类型进行排序。

## 性能

选择排序很好理解，但是它的性能很糟糕，**O(n^2)**。它比 [插入排序](../Insertion%20Sort/README-CN.markdown) 要糟糕的多，但是比 [冒泡排序](../Bubble%20Sort/README-CN.markdown) 要好。杀手就是找到剩下数组中的最小元素，这个花费了很多时间，尤其是内层循环要不断地进行。

[堆排序](../Heap%20Sort/README-CN.markdown) 使用的是和选择排序类似的规则，但是用了一个相当快的方法来找到剩下数组中的最小值。它的性能是 **O(n log n)**。

## 参考

[选择排序 维基百科](https://en.wikipedia.org/wiki/Selection_sort)

*作者：Matthijs Hollemans 翻译：Daisy*


