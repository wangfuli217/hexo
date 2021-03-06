---
title: 关于JS按位异或运算符
tags:
  - web前端
categories:
  - 技术日志
  - JS基础
date: 2019-08-31 16:10:28
---

每当接触一门新的编程语言，一般都是先学变量，之后运算符，最后`if-else`等条件语句。基本上一门编程语言的基础就这些了，而对于 JS 运算符这方面的学习，一般都是扫一眼就过去了，直到前两天做了一道题，才意识到运算符的强大，题目大意是这样的：

> Given an array, find the int that appears an odd number of times.
> There will always be only one integer that appears an odd number of times.
>
> > 以一个纯数字数组作为参数，找出该数组中出现奇数次的数字（该数字有且只有一个）

首先写出了一种常规的解法：

```javascript
function findOdd(array) {
  for (let i = 0; i < array.length; i++) {
    const element = array[i];
    let counter = 0;

    for (let j = 0; j < array.length; j++) {
      if (element === array[j]) counter++;
    }

    if (counter % 2 !== 0) return element;
  }
}
```

这个思路很常规，设定一个计数器，遍历整个数组，最后通过计数器的判断来找出为奇数的元素。

然而，答案通过之后翻了翻赞数比较高的答案，发现一个答案只有一行代码：

```javascript
const finOdd = array => array.reduce((v, i) => v ^ i);
```

看到这个答案第一眼就很懵 b，`^`运算符还能这样用？之后我去找了几篇资料，发现运算符真是大有用处。

## ^ 运算符

> 异或是一种基于二进制的位运算，用符号 XOR 或者 `^` 表示，其运算法则是对运算符两侧数的每一个二进制位，同值取 0，异值取 1。

其实就是 0 + 0 = 0；0 + 1 = 1； 1 + 1 = 0，和不进位的加法很像。

### 满足的运算律

- 交换率
- 结合律
- 任何整数按位异或自己都是自己，按位异或 0 都是零，因为计算机都是二进制存储数据的，转换一下就很好理解了

![](Jietu20190831-164530.jpg)

- 自反性，一个数连续按位异或两个相同的数会返回他自己本身

这个就是按照结合律，先算后边两个相同的就是 0，之后根据第三条就可以得出来了。

![](Jietu20190831-164843.jpg)

### 应用

最主要的应用就是刚开始那一道题，这道题虽然解法很简单，就一行代码，但是问题在于怎么证明它是可行的。

- 基础：`Array.reduce()`方法
- 目的：找出个数为奇数的元素
- 思路: 根据按位异或运算符的性质 3 和性质 1，偶数个数无论有多少个，其实最后异或的结果只有一个（就是它本身），就可以将他们全部异或，假设数组是这样的`[1,1,2,2,3,3,4,4,5,5,5]`，可以将这些数字分为 n 组（n 肯定是偶数）一样的数字集合，出现偶数次的数字只可能被分完或者剩下偶数个数字，根据性质 4 自反性，不影响计算，将他们全部异或后剩下的就是出现次数为奇数的那个数。

类似的题都可以这样去思考实现，例如：

> 有一个数组中存着 1 到 100，现在向里面加入一个未知整数，如何得出新加入进去的数字

这个问题也可以用这种方法来实现。

第二个应用就是可以交换两个变量的值：

![](Jietu20190831-171340.jpg)

当然，还有更简单的方法：

```javascript
b = a + 0 * (a = b); //利用运算符优先级

[a, b] = [b, a]; // ES6解构赋值
```

第三个就是可以用来判断一个数字是不是整数：

> 将任一整数值`x`与`0`进行按位或或者异或操作，其结果都是`x`。如果该值为小数的话，会向下取整。将任一数值`x`与`-1`进行异或操作，其结果为`~x`(`~`表示取反)。

其他的应用就涉及到数据加密传输等。

## 其他运算符的妙用

### 使用`&`运算符判断一个数的奇偶

运算规律为有一个是 0 结果就是 0。

```JavaScript
偶数 & 1 = 0
奇数 & 1 = 1
console.log(5 & 1)    // 0
console.log(6 & 1)    // 1
```

### 使用`~`, `>>`, `<<`, `>>>`, `|`来取整。

`|`运算规律为有一个是 1 结果就是 1。
`~` 运算规律为 `x + ~x = -1`

```JavaScript
console.log(~~ 3.14)    // 3
console.log(3.14 >> 0)  // 3
console.log(3.14 << 0)  // 3
console.log(3.14 | 0)   // 3
// >>>不可对负数取整
console.log(-3.14 >>> 0)   // 4294967293
```

### 关于`~`

一般来说，使用`~`运算符，计算机内部有以下三个步骤：

> - 把运算数转换成`32`位二进制数字
> - 把二进制数转换成它的二进制反码（`0 => 1`, `1 => 0`）
> - 把二进制数转换成浮点数

该运算符可以用来字符串转数字（解析为十进制），判断值是否存在，代替`parseInt(value)`（操作浮点数时）。

注意：此处有坑，不要在转换的字符串或者数字前有八进制的前缀（`0`），会当成八进制转换，其他进制前缀同理。只不过八进制前缀是`0`这个挺坑的，所以在`ES6`中被禁了。

```JavaScript
// 判断元素是否在数组中存在
if(~arr.indexOf(v)){...}
if(arr.indexOf(v) > -1){...}
// 操作浮点数，主要是效率更高
~~ -3.14  // -3
```

### 使用&, >>, |来完成 rgb 值和 16 进制颜色值之间的转换

```js
/**
 * 16进制颜色值转RGB
 * @param  {String} hex 16进制颜色字符串
 * @return {String}     RGB颜色字符串
 */
function hexToRGB(hex) {
  var hexx = hex.replace('#', '0x');
  var r = hexx >> 16;
  var g = (hexx >> 8) & 0xff;
  var b = hexx & 0xff;
  return `rgb(${r}, ${g}, ${b})`;
}

/**
 * RGB颜色转16进制颜色
 * @param  {String} rgb RGB进制颜色字符串
 * @return {String}     16进制颜色字符串
 */
function RGBToHex(rgb) {
  var rgbArr = rgb.split(/[^\d]+/);
  var color = (rgbArr[1] << 16) | (rgbArr[2] << 8) | rgbArr[3];
  return '#' + color.toString(16);
}
// -------------------------------------------------
hexToRGB('#ffffff'); // 'rgb(255,255,255)'
RGBToHex('rgb(255,255,255)'); // '#ffffff'
```

## 总结

编程语言的位运算符其实就是根据计算机中数据的存储方式而生的一种运算方法，根据不同运算符的不同功能，可以做不同的运算与应用。
