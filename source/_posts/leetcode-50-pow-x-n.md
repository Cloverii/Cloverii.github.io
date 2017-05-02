---
title: LeetCode 50. Pow(x, n) 中的小 trick
date: 2017-05-02 19:49:17
categories: LEARNING
tags: [Algorithm, LeetCode]
---

一句话概括：这题很多 [solutions](https://leetcode.com/problems/powx-n/#/solutions) 有问题，但是没有办法卡掉错的 solutions。

[题目链接](https://leetcode.com/problems/powx-n/#/description)

> Implement pow(*x*, *n*).

<!--more-->
### 一开始的写法和 trick
函数原型如下。底数为 double 类型，指数为 int 类型。
```c++
double myPow(double x, int n)
```
显然写个快速幂就好了，但是有一个 trick，n 可能为负数。这也很好办，加个 abs() 就行。但这又引发了一个新问题：n = INT_MIN 的时候，由于第7行 `long long nn = abs(n * 1LL);` 有一个 abs() 操作，所以要先把 n 强转成 long long 再 abs()，不然 abs(INT_MIN) = INT_MAX + 1（至于为什么会是这样，自行 Google 计算机中有符号数的存储形式——补码），这个数不在 int 的表示范围内，会发生溢出。我的习惯是乘上一个 1LL 代替显式转换。现在问题来了，为什么[这份 solution](https://discuss.leetcode.com/topic/17832/non-recursive-c-log-n-solution) 写 `unsigned long long p = -n;` 这种违反直觉的代码居然还能过呢？这里先向被我挂出来的这位老兄说句不好意思，并不是针对他……
```c++
class Solution { // This is my AC code
public:
    double myPow(double x, int n) {
        double base = x;
        if(n < 0) base = 1 / x;
        x = 1.0;
        long long nn = abs(n * 1LL); // unsigned long long p = -n; is wrong!
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
本来应该到此结束了的，但是我又瞅了瞅[其他 solutions](https://discuss.leetcode.com/topic/3636/my-answer-using-bit-operation-c-implementation)，意识到了一个问题：如果把~~装13的~~位运算换成正经的 % 和 /，就没有必要对 n 进行 abs() 了。所以代码可以写成这样：
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

话说用位运算代替除法还是写 ACM 代码留下的后遗症……看来今后还是得长点心，毕竟负数情况下表现就不一致了。而且这样装模作样地替编译器优化并没有什么实际意义。学了体系结构，对编译器优化的理解确实又上了一个层次。

### 其他参考

https://gcc.gnu.org/wiki/NewWconversion
http://stackoverflow.com/questions/4975340/int-to-unsigned-int-conversion



