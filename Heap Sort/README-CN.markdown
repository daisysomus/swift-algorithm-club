# 堆排序

使用堆将数组从低到高进行排序。

[堆](../Heap/README-CN.markdown) 是存储在数组内部的一个部分有序的二叉树。堆排序算法利用堆结构来执行一个快速的排序。

排序是从低到高的，堆排序先将未排序的数组变成一个最大堆，所以数组的第一个元素就是最大的。

假设要排序的数组是：

	[ 5, 13, 2, 25, 7, 17, 20, 8, 4 ]

下面是第一次变成最大堆的结果：

![The max-heap](Images/MaxHeap.png)

堆的内部数组是这样的：

	[ 25, 13, 20, 8, 7, 17, 2, 5, 4 ]

这很难叫做是有序的！但是现在排序过程才开始：将第一个元素（索引 *0*）和索引 *n-1* 的元素交换，得到：

	[ 4, 13, 20, 8, 7, 17, 2, 5, 25 ]
	  *                          *

现在新的根节点是 4 ，它比它的子节点都要小，所以我们要用 *变换下去* 或者 *堆起来* 的过程来修复最大堆到 *n-2* 的元素。修复之后，新的根节点是数组中第二大的元素：

	[20, 13, 17, 8, 7, 4, 2, 5 | 25]

注意：当我们修复堆的时候，我们忽略索引 *n-1* 位置的最大元素。它现在包含的是数组的最大值，所以这就是它所在的地方。`|` 符号表示的是已排序数组开始的地方。从现在开始就不管这部数组了。

再一次，将第一个元素和最后（这次是 *n-2*）一个元素进行互换：

	[5, 13, 17, 8, 7, 4, 2, 20 | 25]
	 *                      *

然后再修复它，使它变成一个最大堆：

	[17, 13, 5, 8, 7, 4, 2 | 20, 25]

正如你看到的，最大的元素找到了到后面去的方法。重复这个过程直到根节点并且整个数组是有序的了。

> **注意：** 这个过程和选择排序非常类似，[选择排序](../Selection%20Sort/README-CN.markdown)是不断地从剩下的数组中查找最小的元素。分离最小和最大元素是堆所擅长的。

堆排序的时间复杂度在所有情况下都是是 **O(n lg n)**。因为我们是直接修改数组，所以堆排序是原地算法。但他不是稳定排序：相同元素的相对位置是不能维持的。

下面是在 Swift 中如何实现堆排序：

```swift
extension Heap {
    public mutating func sort() -> [T] {
        for i in stride(from: (elements.count - 1), through: 1, by: -1) {
            swap(&elements[0], &elements[i])
            shiftDown(0, heapSize: i)
        }
    return elements
    }
}
```

这是给我们的[堆](../Heap/README-CN.markdown)实现添加了一个 `sort()` 函数。可以像下面这样使用它：

```swift
var h1 = Heap(array: [5, 13, 2, 25, 7, 17, 20, 8, 4], sort: >)
let a1 = h1.sort()
```

因为我们需要一个最大堆来从低到高进行排序，所以需要给 `Heap` 一个相反的排序函数。为了对 `<` 排序， `Heap` 对象创建 `>` 作为排序方法。换句话说，从低到高需要创建一个最大堆，相反的就要一个最小堆。

可以写一个非常方便的辅助方法：

```swift
public func heapsort<T>(_ a: [T], _ sort: @escaping (T, T) -> Bool) -> [T] {
    let reverseOrder = { i1, i2 in sort(i2, i1) }
    var h = Heap(array: a, sort: reverseOrder)
    return h.sort()
}
```

*作者：Matthijs Hollemans 翻译：Daisy*


