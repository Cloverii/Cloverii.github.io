---
title: LeetCode 50. Pow(x, n) 与二进制中1的个数
date: 2017-05-02 19:49:17
categories: ALGORITHM
tags: [LeetCode]
toc: true
---

1. 这题很多 [solutions](https://leetcode.com/problems/powx-n/#/solutions) 有问题，但是没有办法卡掉。
2. 需要用到 abs() 的时候记得考虑 INT_MIN, 且有符号数溢出是 undefined behavior.
3. 有符号数右移是 implementation-dependent 的行为。
   `0xFFFFFFFF(-1) >> 1`, 如果实现是算术右移的话，会导致死循环。
4. 当负数转换为无符号类型时，得到的值应该是原数加上 2^n, n 是该无符号类型的位数。
5. `lowbit()` 可以有一些巧妙的应用。

[题目链接](https://leetcode.com/problems/powx-n/#/description)

> Implement pow(*x*, *n*).

<!--more-->
### 一开始的写法和 trick 

**不要参考这份代码！**

函数原型如下。底数为 double 类型，指数为 int 类型。
```c++
double myPow(double x, int n)
```
显然写个快速幂就好了，但是有一个 trick，n 可能为负数。这也很好办，加个 abs() 就行。但这又引发了一个新问题：n = INT_MIN 的时候，由于第7行 `long long nn = abs(n * 1LL);` 有一个 abs() 操作，所以要先把 n 强转成 long long 再 abs()，不然 `abs(INT_MIN) = INT_MAX + 1`（至于为什么会是这样，自行 Google 计算机中有符号数的存储形式——补码），这个数不在 int 的表示范围内，会发生溢出。我的习惯是乘上一个 1LL 代替显式转换。现在问题来了，为什么[这份 solution](https://discuss.leetcode.com/topic/17832/non-recursive-c-log-n-solution) 写 `unsigned long long p = -n;` 这种违反直觉的代码居然还能过呢？这里先向被我挂出来的这位老兄说句不好意思，并不是针对他……
```c++
class Solution { // This is my AC code
public:
    double myPow(double x, int n) {
        double base = x;
        if(n < 0) base = 1 / x;
        x = 1.0;
        long long nn = abs(1LL * n); // unsigned long long p = -n; is wrong!
        while(nn != 0) {
            if(nn & 1)
                x *= base;
            base *= base;
            nn >>= 1;
        }
        return x;
    }
};
```
程序猿都知道，溢出这个东西非常的奇妙。无符号数溢出的行为编译器是有定义的，但有符号数溢出是一个 undefined behavior，依赖于编译器实现。我在 LeetCode 上测试 abs(INT_MIN) 的结果是 INT_MIN 本身（会导致程序死循环），但这个结果并没有任何意义。

事实上，GCC 编译器总是假定有符号数溢出不会发生，导致了另外一些有趣的现象。比如[这个优化的问题](http://stackoverflow.com/questions/22798709/g-strict-overflow-optimization-and-warnings)。

下一个问题：既然在 LeetCode 的编译器下，-INT_MIN=INT_MIN，这个时候 unsigned long long p 的值是多少呢？

好消息是，这个行为也是有定义的。当负数转换为无符号类型时，得到的值应该是原数加上 2^n，n 是该无符号类型的位数。

```c++
int i = -2;
unsigned int j = i;
// 18446744073709551614
```

事实上，从负数到无符号类型的隐式类型转换会引起 warning，编译 C++ 时打开 `-Wsign-conversion` 编译选项就可以观察到：

```c++
A.cpp: In function ‘int main()’:
A.cpp:9:28: warning: conversion to ‘long long unsigned int’ from ‘int’ may change the sign of the result [-Wsign-conversion]
     unsigned long long j = i;
                            ^
```

所以这份 solution 通过把 p 声明成 unsigned long long 成功地避开了无限循环，但得到的也并不是期望的结果，那为什么能 A 呢？思考一下就会发现，当指数为 INT_MIN 时，除了底数为 1.0 时结果为 1，其他情况结果不是 0 就是 inf，所以根本没有可以卡掉这个错误写法的数据……所以，就这样吧……

### 用 % 和 / 代替位运算
本来应该到此结束了的，但是我又瞅了瞅[其他 solutions](https://discuss.leetcode.com/topic/3636/my-answer-using-bit-operation-c-implementation)，意识到了一个问题：如果把~~装13的~~位运算换成正经的 % 和 /，由于除法是向 0 取值，就没有必要对 n 进行 abs() 了。所以代码可以写成这样：
```c++
class Solution {
public:
    double myPow(double x, int n) {
        double base = x;
        if(n < 0) base = 1 / x; // no need for n = -n;
        x = 1.0;
        while(n != 0) {
            if(n % 2)
                x *= base;
            base *= base;
            n /= 2;
        }
        return x;
    }
};
```
注意上面的链接里有的解法出现了求 -n 的语句，但是我认为加上这句代码毫无意义，除了能说明作者并不懂类型转换。

又查了查，发现有符号数右移也是 implementation-dependent，所以自己还是太想当然了……话说用位运算代替除法还是写 ACM 代码留下的后遗症……看来今后还是得长点心啦，而且这样装模作样地替编译器优化并没有什么实际意义。

感觉需要重读 K&R 了耶。

### 逻辑移位和算术移位
在阅读这部分之前，你最好对[补码](https://en.wikipedia.org/wiki/Two%27s_complement)有一点了解。

- 逻辑移位：移位后空缺部分填0
- 算术移位：算术左移同逻辑左移，算术右移空缺部分按最左边的一位（通常为符号位）填充

[这里有个回答](https://stackoverflow.com/a/22734721) 总结得很好，我就直接贴了。

C/C++ 不像 Java 那样有两种右移操作符。在 C/C++中，无符号数移位显然是逻辑移位，而有符号数的右移操作则是 implementation-dependent, 大部分编译器实现为算术右移。当然，由于正数的符号位是0, 两种移位方式得到的结果并没有区别。

查资料的过程中也看到有人讲嵌入式开发里通常用 `unsigned int` 来保证平台无关。

### 数二进制中 1 的个数

> 请实现一个函数，输入一个整数，输出该数二进制表示中 1 的个数。例如把 9 表示成二进制是 1001, 有 2 位是 1. 因此如果输入9, 该函数输出 2. （来自《剑指 offer》面试题10）

经过上面的讨论，显然下面这种写法在`n = -1`时是会死循环的：
```c++
int numberOf1(int n) {
    int count = 0;
    while(n) {
        if(n & 1)
            ++count;
        n = n >> 1;
    }
    return count;
}
```
而如果我们不右移`n`, 而是将 1 每次左移，就可以绕开这个 trick: 
```c++
int numberOf1(int n) {
    int count = 0;
    unsigned int flag = 1;
    while(flag) {
        if(n & flag)
            ++count;
        flag = flag << 1;
    }
    return count;
}
```
还有一个更妙的解法是利用树状数组常用的`lowbit()`, `n & (n - 1)`可以把`n`最右边的 1 变 0:
```c++
int numberOf1(int n) {
    int count = 0;
    while(n) {
      ++count;
      n = n & (n - 1);
    }
    return count;
}
```
顺便说下 gcc 是有 `__builtin_popcount()` 这种东西的，当然这个实现就非常有趣了。附个[网址](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=36041).


### 其他参考

https://gcc.gnu.org/wiki/NewWconversion
https://en.wikipedia.org/wiki/Arithmetic_shift
https://en.wikipedia.org/wiki/Logical_shift
https://zh.wikipedia.org/wiki/%E4%BD%8D%E6%93%8D%E4%BD%9C
http://stackoverflow.com/questions/4975340/int-to-unsigned-int-conversion
http://stackoverflow.com/questions/7622/are-the-shift-operators-arithmetic-or-logical-in-c
http://blog.csdn.net/tandesir/article/details/7385955
何海涛. 剑指offer[Z]. 北京: 电子工业出版社,2014.