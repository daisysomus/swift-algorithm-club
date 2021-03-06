# 插入排序

目标：将一个数组从低到高（或从高到低）进行排序。

给你一个包含数字的数组，需要将它们按照正确的顺序来进行存放。插入排序的算法如下：

- 有一组数字，这一组数字是没有排序的。
- 从中选择一个数字。选择哪一个并不会影响算法，最简单的方式就是选取第一个数字。
- 将这个数字放到一个新的数组中。
- 从未排序的数字堆中再拿一个数字，然后插入到新数组。这个数字要么是插入到第一个数字的前面要么是后面，然后这两个数字就是排好序的了。
- 再从数字堆中拿一个数字插入到前面已经排好序的数组中。
- 重复上面的步骤直到没有需要插入的数字。最后的结果是数字堆中没有了数字，得到了一个排好序的数组。

这就是为什么“插入”排序的原因，因为每次都是从堆中拿一个数字然后插入到数组中合适的位置。

## 例子

有一组未排序的数字： `[8, 3, 5, 4, 6]`。

取第一个数字：`8`，然后将它插入到一个新的数组。这时数组中还没有任何数字，所以插入非常简单。现在数组中是 `[8]`，数字堆就变成了 `[ 3, 5, 4, 6 ]`。

从堆中拿下一个数字 `3` 插入到有序数组中。它应该要插在 `8` 前面，所以现在有序数组就变成了 `[ 3, 8 ]`，数字堆中的数字变成了 `[ 5, 4, 6 ]`。

再取下一个数字 `5` 插入到有序数组中。它要插入在 `3` 和 `8` 中间。有序数组就变成了 `[ 3, 5, 8 ]`，数字堆中就变成了 `[ 4, 6 ]`。

一直重复上面的过程直到没有可插入的数字。

## 原地排序

上面的解释看起来我们需要两个数组，一个用来存放未排序的数字，另一个用来存放已排序的数字。

但是我们可以将插入排序实现成“原地”，并不需要创建一个单独的数组。我们只要跟踪数组的哪部分是已排好序的，哪部分是未排好序的。

刚开始，数组是 `[ 8, 3, 5, 4, 6 ]`。`|` 是已排序和未排序的分隔线，`|`前面是已排序的，后面是未排序的。

	[| 8, 3, 5, 4, 6 ]

上面表示的是还没有已排序的，数字堆的开始位置在 `8`。

处理了第一个数字之后，我们有了下面的数组：

	[ 8 | 3, 5, 4, 6 ]

排好序的部分是 `[ 8 ]`，未排序的是 `[ 3, 5, 4, 6 ]`。`|` 符号向右挪动了一个位置。

下面是数组中内存随着排序变化的过程：

	[| 8, 3, 5, 4, 6 ]
	[ 8 | 3, 5, 4, 6 ]
	[ 3, 8 | 5, 4, 6 ]
	[ 3, 5, 8 | 4, 6 ]
	[ 3, 4, 5, 8 | 6 ]
	[ 3, 4, 5, 6, 8 |]

在每一步中， `|` 都移动一个位置。就像我们看到的，一开始 `|` 前面的数组总是已排序的。随着排序的进行，数组堆会逐渐缩小，已排序的数组会逐渐变大，直到堆变空并且没有需要排序的数字。

## 如何插入

每一次从堆顶拿一个数字插入到已排序数组中时，都必须要将数字放在已排序数组的合适位置以保证已排序数组一直都是有序的。那么这个又是如何工作的呢？

假如我们已经完成了前面几个元素的排序，现在数组是下面这样的：

	[ 3, 5, 8 | 4, 6 ]

下一个要排序的数字是 `4`。我们要将它插入到已经排好序的 `[ 3, 5, 8 ]` 中的某一处。

下面是一种实现方式：查找前一个元素： `8`。

	[ 3, 5, 8, 4 | 6 ]
	        ^
	        
它是不是比 `4` 大？使得，所以 `4` 要在 `8` 的前面。我们将这两个数进行交换：

	[ 3, 5, 4, 8 | 6 ]
	        <-->
	        交换

到这里还没有结束。下一个元素是 `5`，它也比 `4` 大。我们依然要交换这两个数：

	[ 3, 4, 5, 8 | 6 ]
	     <-->
	     交换

继续查看前一个元素。`3` 比 `4` 大吗？没有，这就意味着我们完成了对数字 `4` 的操作。数组依然是有序的。

上面的是插入排序算法中内部循环的描述，下面我们要看看看看怎么从堆顶将数字插入到有序数组中。

## 代码

下面是一个用 Swift 实现的插入排序：

```swift
func insertionSort(_ array: [Int]) -> [Int] {
  var a = array                             // 1
  for x in 1..<a.count {                    // 2
    var y = x
    while y > 0 && a[y] < a[y - 1] {        // 3
      swap(&a[y - 1], &a[y])
      y -= 1
    }
  }
  return a
}
```

将代码放到 playground 中，然后用下面的代码来测试：

```swift
let list = [ 10, -1, 3, 9, 2, 27, 8, 5, 1, 3, 0, 26 ]
insertionSort(list)
```

下面就说说代码是如何工作的。

1. 先创建一个数组的靠背。因为我们不能直接修改参数 `array` 中的内容，所以这是非常必要的。`insertionSort()` 会返回一个原始数组的*拷贝*，就像 Swift 自己的 `sort()` 方法一样。

2. 在函数里有两个循环，外层的循环依次查找数组中的每一个元素；这就是从数字堆中取最上面的数字的过程。变量 `x` 是有序部分结束和堆开始的索引（也就是 `|` 符号的位置）。要记住的是，在任何时候，从 9 到 `x` 的位置数组都是有序的，剩下的则是无序的。

3. 内存循环则查找 `x` 位置的元素。这就是堆顶的元素，它有可能比前面的所有元素都小。内存循环从有序数组的后面开始往前找。每次找到一个比它的元素，就交换它们的位置，直到内层循环结束，数组的前面部分依然是有序的，有序的元素也增加了一个。 

> **注意：** 外层循环是从 1 开始，而不是0。从堆顶将第一个元素移动到有序数组没有任何意义，所以我们跳过这一步。 

## 不需要交换

上面的插入排序算法可以很好的完成任务，但是我们也可以移除对 `swap()` 的调用来让它更快。

我们是通过交换两个数字来让下一个元素放到合适的位置的：

	[ 3, 5, 8, 4 | 6 ]
	        <-->
            swap
	        
	[ 3, 5, 4, 8 | 6 ]
         <-->
	     swap

我们可以通过将这些元素往右挪一个位置来代替元素的交换，然后将新的数字放到正确的位置。

	[ 3, 5, 8, 4 | 6 ]   记录 4
	           *
	
	[ 3, 5, 8, 8 | 6 ]   将 8 往右移
	        --->
	        
	[ 3, 5, 5, 8 | 6 ]   将 5 往右移
	     --->
	     
	[ 3, 4, 5, 8 | 6 ]   将 4 拷贝过来
	     *

代码里是这样的：

```swift
func insertionSort(_ array: [Int]) -> [Int] {
  var a = array
  for x in 1..<a.count {
    var y = x
    let temp = a[y]
    while y > 0 && temp < a[y - 1] {
      a[y] = a[y - 1]                // 1
      y -= 1
    }
    a[y] = temp                      // 2
  }
  return a
}
```

`//1` 这行代码就是将前一个元素往右移动一个位置，在内层循环结束的时候， `y` 就是新数字在有序数组中的位置， `//2` 这行代码就是将数字拷贝到正确的地方。

## 泛型化

如果能排序除了数字之外的东西就更好了。我们可以使数组的数据类型泛型化，然后使用一个用户提供的函数（或闭包）来执行比较操作。这仅仅只要改变两个地方。

函数变成这样了：

```swift
func insertionSort<T>(_ array: [T], _ isOrderedBefore: (T, T) -> Bool) -> [T] {
```

数组有一个类型 `[T]`，`[T]` 是泛型化的一个占位类型。现在 `insertionSort()` 可以接收任何类型的数组，不管它是包含数字、字符串或者别的什么东西。

新的参数 `isOrderedBefore: (T, T) -> Bool` 是一个接收两个 `T` 对象然后返回一个 `Bool` 值的方法，如果第一个对象大于第二个，那么返回 `true`，反之则返回 `false`。这与 Swift 内置的 `sort()` 方法是一样的。

另外一个变化就是内层循环，现在应该是这样的：

```swift
      while y > 0 && isOrderedBefore(temp, a[y - 1]) {
```

我们用调用 `isOrderedBefore()` 函数的方式替代了之前的 `temp < a[y - 1]`。除了我们现在可以比较任何类型的对象，而不仅仅是数字之外，它们做的事情是一样的。

在 playground 中进行测试：

```swift
let numbers = [ 10, -1, 3, 9, 2, 27, 8, 5, 1, 3, 0, 26 ]
insertionSort(numbers, <)
insertionSort(numbers, >)
```

`<` 和 `>` 决定排序的顺序，分别代表低到高和高到低。

当然，我们也可以排布像字符串一样的数据：

```swift
let strings = [ "b", "a", "d", "c", "e" ]
insertionSort(strings, <)
```

甚至更复杂的对象：

```swift
let objects = [ obj1, obj2, obj3, ... ]
insertionSort(objects) { $0.priority < $1.priority }
```

闭包告诉 `insertionSort()` 方法用 `priority` 属性来进行排序。

插入排序是一个 *稳定* 的排序算法。当元素相同时，排序后依然保持排序之前的相对顺序，那么这个排序算法就是 *稳定* 的。对于像数字或者字符串这样的简单类型来说，这不是很重要，但是对于复杂的对象来说，这就很重要了。如果两个对象有相同的 `priority`， 不管它们其他的属性如何，这两个对象都不会交换位置。

## 性能

如果数组是已经排好序的话，插入排序是非常快速的。这听起来好像很明显，但是不是所有的搜索算法都是这样的。在实际中，有很多数据（大部分，可能不是全部）是已经排序好的，插入排序在这种情况下就是一个非常好的选择。

插入排序的最差和平均表现是 **O( n^2 )**。这是因为在函数里有两个嵌套的循环。其他如快速排序和归并排序的表现则是 **O(n log n)**，在有大量输入的时候会更快。

插入排序在对小数组进行排序的时候实际是非常快的。一些标准库在数据量小于或者等于10的时候会从快速排序切换到插入排序。

我们做了一个速度测试来对比我们的 `insertionSort()` 和 Swift 内置的 `sort()`。在大概有 100 个元素的数组中，速度上的差异非常小。然后，如果输入一个非常大的数据量， **O(n^2)** 马上就比 **O(n log n)** 表现的糟糕多了，插入排序远远比不上。

## 扩展阅读

[插入排序 维基百科](https://en.wikipedia.org/wiki/Insertion_sort)

*作者：Matthijs Hollemans 翻译：Daisy*


