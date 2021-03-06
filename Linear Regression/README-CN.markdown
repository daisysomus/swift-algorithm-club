# 线性回归

线性回归是用来创建两个（或多个）变量之间关系模型的技术。

例如，加入我们要打算卖一辆汽车。我们不知道应该要多少钱。所以我们就看看其他车的最近的要价广告。我们需要查找很多变量 - 例如：制造商、型号、引擎大小。为了简化工作，我们还需收集车龄以及它的价格：

车龄 (年)| 价格 (£)
--------------|-------------
10 | 500
8 | 400
3 | 7,000
3 | 8,500
2 | 11,000
1 | 10,500

我们的车龄是 4 年。如何基于上面的价格来设置我们的车的价格呢？

让我们先从画出的数据开始：

![graph1](Images/graph1.png)

我们可以想象有一根线穿过这个表上的店。不一定要穿过每一个点，但是我们将这条线放到尽量靠近所有点的地方。

换种说法就是，尽量让每个点到这条线的距离都短。这通常是通过最小化从线到点的距离的平方来完成的。

我们可以用连个变量来描述这条直线：

1. 经过 y 轴的点。即新车的预测价格。这就是 *截距*
2. 线的 *斜率* - 即对于每一个年限，价格会变化多少。

这就是我们的线的等式：

`车的价格 = 斜率 * 车龄 + 截距`

如何找到截距和斜率的最佳值呢？让我们来看看两个不同的方法。

## 迭代方式
这个方式是从一些随机的截距和斜率斜率值开始的。然后通过轻微地改变这些值来使得线靠近数据点。然后不断地重复这个过程。最后线回到达一个最优的位置。

首先构建我们的数据结构。用两个 Swift 数组来表示车龄和车的价格：

```swift
let carAge: [Double] = [10, 8, 3, 3, 2, 1]
let carPrice: [Double] = [500, 400, 7000, 8500, 11000, 10500]
```

下面是表示直线的方法：

```swift
var intercept = 0.0
var slope = 0.0
func predictedCarPrice(_ carAge: Double) -> Double {
    return intercept + slope * carAge
}

```
现在就是执行迭代的代码了：

```swift
let numberOfCarAdvertsWeSaw = carPrice.count
let numberOfIterations = 100
let alpha = 0.0001

for n in 1...numberOfIterations {
    for i in 0..<numberOfCarAdvertsWeSaw {
        let difference = carPrice[i] - predictedCarPrice(carAge[i])
        intercept += alpha * difference
        slope += alpha * difference * carAge[i]
    }
}
```

```alpha``` 是确定每次迭代有多靠近正确的解法的因子。如果这个因子太大，那我们的程序可能不会收敛到正确的解决方案。

程序会遍历每个数据点（每个车龄和车的价格）。对于每个数据点都会调整截距和斜率来让他们靠近正确的值。代码中用来调整截距和斜率的等式是基于这些变量的最大减少的方向上的移动。这就叫做 *梯度下降*。

我们想要使得线和点之间的距离最小化。定义一个函数 `J` 来表示距离 - 为了简单起见，我们就先考虑一个点。这个函数是对 `((slope.carAge + intercept) - carPrice)) ^ 2` 的一个均衡。

为了在最大减少的方向上移动，我们采用这个函数相对于斜率的偏导数，对于截距也是一样。将这些导数和 alpha 因子相乘然后用它来调整每次迭代的斜率和截距的值。

看看代码，直觉上是有道理的 - 当前预测价格和实际价格相差越大，```alpha``` 的值就会越大，对于解决和斜率的调整也就越大。

要达到理想的值需要很多次的跌打。我们来看看随着迭代的执行，截距和斜率的值是如何变化的：

迭代 | 截距 | 斜率 | 4 年老车的预测价格
:---------:|:---------:|:-----:|:------------------------:
0 | 0 | 0 | 0
2000 | 4112 | -113 | 3659 
6000 | 8564 | -764 | 5507 
10000 | 10517 | -1049 | 6318 
14000 | 11374 | -1175 | 6673 
18000 | 11750 | -1230 | 6829 

下面是同一个数据的图的表现形式。图中的每条蓝线都表示上面表格的一行。

![graph2](Images/graph2.png)

在 18,000 此迭代之后线好像更接近（通过观察）最合适的那条了。同样的，每个额外的 2000 次迭代对最后的结果的影响会越来越小 - 截距和斜率的已经收到到了正确的值。

##A 封闭的解决方案
 
还有一种方法可以用来计算最合适的线，而不需要很多次迭代。可以解决描述最小二乘最小化的等式来直接得到截距和斜率。

首先我们需要一些辅助函数。这是计算浮点数数组的平均值（中间值）的：

```swift
func average(_ input: [Double]) -> Double {
    return input.reduce(0, +) / Double(input.count)
}
```
用 Swift 里的 ```reduce``` 函数来对数组里所有的元素求和，然后在除以元素的个数。就得到了中间值。

我们还要能将数组中的每个元素与另一个数组中的元素相乘来构建一个新的数组。这个函数就是用来做这个的：

```swift
func multiply(_ a: [Double], _ b: [Double]) -> [Double] {
    return zip(a,b).map(*)
}
```

用 ```map``` 函数来乘以每个元素。

最后，函数会让线来匹配数据：

```swift
func linearRegression(_ xs: [Double], _ ys: [Double]) -> (Double) -> Double {
    let sum1 = average(multiply(ys, xs)) - average(xs) * average(ys)
    let sum2 = average(multiply(xs, xs)) - pow(average(xs), 2)
    let slope = sum1 / sum2
    let intercept = average(ys) - slope * average(xs)
    return { x in intercept + slope * x }
}
```
这个函数传入两个浮点数的数组然后返回一个函数表示一条最适合它的先。这个公式用来计算从函数 `J` 的定义里得出的截距和斜率的值。让我们来看看这个线的输出是如何匹配数据的：

![graph3](Images/graph3.png)

用这条线，我们可以预测 4 年老车的价格是 £6952。


## 总结
我们已经看到了用 Swift 实现的两种不同的线性回归的实现方式。一个明显的问题是：为什么要用迭代的方法？

好吧，我们找到的线并不是很完美。有一件事情，在车龄比较大的时候图里有一些负数得知！可能我们真的要付钱给别人才能让别人拿走一辆老车...但是真实情况是这些负数值表示我们并没有很好地模拟真实的生活情况。车龄和车的价格之间可能不是一个线性的关系而是别的什么函数。我们也知道车的价格并不是只跟车龄有关还跟其他一些比如制造商、型号和车的引擎大小有关。我们还需要用其他的变量来描述这些因素。

结果就是在一些更富在的模型里，迭代方法是唯一可靠或者高效的方法。数据非常大或者数据分布比较稀疏的情况也是可能会发生的。

*作者：James Harrop 翻译：Daisy*


