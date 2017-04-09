---
title: 记录一下我的 GSoC 2017
date: 2017-04-09 09:47:29
categories: OTHERS
tags: Thoughts
toc: true
---

大概在三月底的某一天突然看到了一个叫做 Google Summer of Code 的东西，觉得非常有趣，只是能搜到的中文资料非常非常少，似乎中国参加的人也不多，所以用中文记录一下我今年的经历。

<!--more-->

## 关于 GSoC

[官方说明](https://summerofcode.withgoogle.com/) （链接可能需要梯子）：Google Summer of Code is a global program focused on bringing more student developers into open source software development. Students work with an open source organization on a 3 month programming project during their break from school.

大概就是 Google 出钱鼓励大学生给开源项目写代码的活动，嗯。具体的自行看官网吧。今年中国是 [3600 刀](https://developers.google.com/open-source/gsoc/help/student-stipends)。顺便说一下这个钱数是根据各个国家或地区的[购买力平价](https://zh.wikipedia.org/wiki/%E8%B4%AD%E4%B9%B0%E5%8A%9B%E5%B9%B3%E4%BB%B7)算出来的，最高的比如 UK 有 6600 刀。

**扩展阅读：**

[Student and Mentor Manuals](https://developers.google.com/open-source/gsoc/resources/manual)

[FAQ](https://developers.google.com/open-source/gsoc/faq)

[做一名开源社区的扫地僧 (上)](https://linux.cn/thread-9511-1-1.html)

[欢迎加入全球最苦逼的开源项目 - 从 Google Summer of Code 到 Wine](https://docs.google.com/document/pub?id=1tQkAgjM9rPjRBoqvJ3nZtrAfjScMKO_RxlbPN9pFR4A&embedded=true)

[发现开源社区的扫地僧——Google Summer of Code 2015导师手记（1）](https://tonghuix.io/2015/05/mentor-in-google-summer-of-code-1/)

## 我的经历

以下内容假定你已经读完了 guideline, 了解 GSoC 的流程了。

### 提交学籍证明

官方有[详细说明](https://developers.google.com/open-source/gsoc/help/proof-of-enrollment)。我提交的是有教务处盖章的证明信。这个一般在学校国际处可以开的。我校八教一楼有台自助打印机专门打印这个，so easy - -  虽然我也是第一次知道。

### 选择 organization 和 project ideas

看自身技能和 interest 吧。

### 热身题

现在 organization 似乎都会有一些措施来考察申请者，比如申请之前要 fix 简单的 bug 啊，或者是写个小 demo 之类的。我选择的 [52°North](http://52north.org/) 就有要求必须完成一个 Code Challenge.

我选择的 project idea 是关于 [Protocol Buffers](https://github.com/google/protobuf)（Google 开发的一种数据交换格式） 的。Code Challenge 也是写一个关于 protobuf 的小 demo, 使用 protobuf 将 json 文件序列化再反序列化。看起来很简单,实际也遇到很多问题，直接拖慢了我 proposal 的进度。具体下次再说吧。

### Proposal

官方有给出两份 proposal 的例子：[1](http://write.flossmanuals.net/gsocstudentguide/proposal-example-1/) 和 [2](http://write.flossmanuals.net/gsocstudentguide/proposal-example-2/)。 很多 organization 也会提示 proposal 需要包含的点。我写得太烂就不放了……

### 其他

前阵子忙实习忙得焦头烂额也没有什么结果，一直处于焦虑状态。 忙了一星期的 GSoC 倒是平静下来了，具体表现为又开始花时间在不相干的事情上了，也不知道是好是坏。今年确实知道得太晚了，希望明年还有机会参加吧。

