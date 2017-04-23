---
title: 关于 Java 中的 equals() 和 hashCode()
date: 2017-04-22 13:27:37
categories: LEARNING
tags: [Java]
---

昨天晚上跟韬韬讨论二分写法的时候时，target 和 nums[m] 哪个写在右边的时候，说到了 重载 == 和 < 的问题，然后又说到 Java 的问题。突然发现记不太清 equals() 和 hashcode() 的具体情况了……赶紧查 [Docs](http://docs.oracle.com/javase/8/docs/api/) 记录一下。
<!--more-->
## Introduction
java.lang.Object 中定义了 equals() 和 hashCode() 方法，
## equals()
先无脑引用一下 Docs: 
> #### equals
>
> ```
> public boolean equals(Object obj)
> ```
>
> The `equals` method implements an equivalence relation on non-null object references:
>
> - It is *reflexive*: for any non-null reference value `x`, `x.equals(x)` should return `true`.
> - It is *symmetric*: for any non-null reference values `x` and `y`, `x.equals(y)` should return `true` if and only if `y.equals(x)`returns `true`.
> - It is *transitive*: for any non-null reference values `x`, `y`, and `z`, if `x.equals(y)` returns `true` and `y.equals(z)` returns `true`, then `x.equals(z)` should return `true`.
> - It is *consistent*: for any non-null reference values `x` and `y`, multiple invocations of `x.equals(y)` consistently return `true` or consistently return `false`, provided no information used in `equals` comparisons on the objects is modified.
> - For any non-null reference value `x`, `x.equals(null)` should return `false`.
>
> The `equals` method for class `Object` implements the most discriminating possible equivalence relation on objects; that is, for any non-null reference values `x` and `y`, this method returns `true` if and only if `x` and `y` refer to the same object (`x == y` has the value `true`).
>
> Note that it is generally necessary to override the `hashCode` method whenever this method is overridden, so as to maintain the general contract for the `hashCode` method, which states that equal objects must have equal hash codes.
>
> - Parameters:
>
>   `obj` - the reference object with which to compare.
>
> - Returns:
>
>   `true` if this object is the same as the obj argument; `false` otherwise.

大致翻译一下：

`equals` 方法实现的是非空对象引用的等值关系：

- 自反性：对于任何非空引用值 `x`， `x.equals(x)` 应当返回 `true`.
- 对称性：对于任何非空引用值 `x` 和 `y`, 当且仅当 `y.equals(x)` 返回 true 时，`x.equals(y)` 应当返回 `true`.
- 传递性：对于任何非空引用值 `x`,  `y` 和 `z`, 如果 `x.equals(y)` 返回 true, 且 `y.equals(z)` 返回 true, `x.equals(z)` 应当返回 true.
- 一致性：对于任何非空引用值 `x` 和 `y`, 只要对象用于 `equals` 比较的信息不改变，`x.equals(y)` 应当始终返回 true 或始终返回 false.
- 对于任何非空引用值 `x`, `x.equals(null)` 应当返回 false.
- `Object` 类的 `equals` 方法 an equivalence relationimplements the most discriminating possible equivalence relation on objects; that is, for any non-null reference values `x` and `y`, this method returns `true` if and only if `x` and `y` refer to the same object (`x == y` has the value `true`).