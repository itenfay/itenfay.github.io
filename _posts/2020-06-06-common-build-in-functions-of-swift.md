---
layout: post
title: "Swift 常见内置函数"
author: "chenxing"
date: 2020-06-06
tag: iOS
---

Swift包含了74个内置函数，这里针对几个我常用的内置函数做一些总结。(内置函数是指无需引入任何Module就可以直接使用的函数)

##### 1.断言 assert，如果参数为 ture，则继续，否则抛出异常

*> 示例*

```
let num = 3

//第一个参数为判断条件，第二各参数为条件不满足时的打印信息
assert(num > 3,"num 不大于 3")

//如果断言被触发(num <= 3)，将会强制结束程序，并打印相关信息
assertion failed: num 不大于 3
```

断言可以引发程序终止，主要用于调试阶段。比如下面的情况：

```
* 数组索引越界问题
* 向函数传值，无效值引发函数不能完成相应任务
* Optional类型数据为nil值，引发的程序crash
```

##### 2.获取序列的元素个数 characters.count (~~countElements~~)

*> 示例*

```
let str = "Hello world!"

//打印元素个数
print("count == \(str.characters.count)")

//打印结果
 count == 3
```

##### 3.将原有序列转换成以元组作为元素的序列输出 enumerated()

*> 示例*

```
let arr = ["J", "K"]

//打印新序列
for (i,j) in arr.enumerated() {
    print("\(i):\(j)")
}

//打印结果
0:J
1:K
````

##### 4.返回最大最小值 min(), max()

*> 示例*

```
min(2, 6)  //返回 2
max(1, 3, 8)  //返回 9
```

##### 排序 sorted (~~sort~~)

*> 示例*

```
let arr = ["X", "Y", "Z"]

//默认排序(升序)
let arr 1 = arr.sorted()
print(arr1)

//打印结果
["X", "Y", "Z"]

//自定义排序(降序)
let arr2 = ary.sorted {
    $1 < $0
}
print(arr2)

//打印结果
["Z", "Y", "X"]
```

##### 6.map 函数

*> 示例*

```
let arr = [2,1,3]

//数组元素进行2倍放大
let doubleArr = arr.map {$0 * 2}
print(doubleArr)

//打印结果
[4, 2, 6]

//数组Int转String
let moneyArr = arr.map { "¥\($0 * 2)"}
print(moneyArr)

//打印结果
["¥4", "¥2", "¥6"]

//数组转成元组
let groupArr = arr.map {($0, "\($0)")}
print(groupArr)

//打印结果
[(2, "2"), (1, "1"), (3, "3")]
```

##### 7.flapMap 函数

*> 示例*

```
let arr  = [["B", "A", "C"], ["1","5"]]

//flapMap函数会降低维度
let flapMapArr = arr.flatMap{$0}
print(flapMapArr)

//打印结果
["B", "A", "C", "1", "5"] // 二维数组变成了一维
```

##### 8.筛选函数 filter

*> 示例*

```
let numbers = [1, 2, 3, 4, 5, 6]

//获取偶数值
let evens = numbers.filter{$0 % 2 == 0}
print(evens)

//打印结果
[2, 4, 6]
```

##### 9.reduce 函数

*> 示例*

```
let arr = [1, 2, 4]

//对数组各元素求和
let sum = arr.reduce(0) {$0 + $1}
print(sum)

//打印结果
7

//对数组各元素求积
let product =  arr.reduce(1) {$0 * $1}
print(product)

//打印结果
8
```
##### 10.在编程中经常会用到的一些函数

```
1. abs(-1) == 1 //获取绝对值
2. ["1","6","4"].contains("2") == false  //判断序列是否包含元素
3. ["a","b"].dropLast() == ["a"]  //剔除最后一个元素
4. ["a","b"].dropFirst() == ["b"] //剔除第一个元素
5. ["a","b"].elementsEqual(["b","a"]) == false  //判断序列是否相同
6. ["a","b"].indices == 0..<2  //获取index(indices是index的复数)
7. ["A", "B", "C"].joined(separator: ":") == "A:B:C" //将序列以分隔符串联起来成为字符串
8. Array([2, 7, 0].reversed()) == [0, 7, 2]   //逆序，注意返回的并非原类型序列
```
