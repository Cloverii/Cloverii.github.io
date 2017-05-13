---
title: Python 模拟登录教务网获取课表
date: 2017-05-04 21:03:25
categories: TECH
tags: Python
---
### 写在前面的废话
说到查看课表，我估计大多数人还是用的超级课程表跟课程格子。超级课程表我大一刚入学的时候还用过来着，但是实在是太没节操了以致于我怀疑它是不是放弃了女性使用者。相对来说课程格子界面清爽很多，功能也够用，只是没课的时候点击 widget 默认进入“格子BBS”，让人很不爽，后来更是多了很多奇怪的推送。可我只想安安静静地看个课表啊……为什么天朝的所有 APP 都要做社交呢？

大二的时候我把手机换成了 Nexus 5X（没错，就是 LG 代工的那款升级 Nougat 之后爆了硬件问题可以全额退款的手机，可惜人在大陆没办法，于是这块砖现在还静静地躺在书架上。话题好像偏了……）总之我从换了手机之后开始用 Google Calendar 代替原本的课表 APP，非常完美。唯一的缺点是，每学期开学需要手动导入，懒癌患者又不想学 Python. ljf 聚聚说好的添课脚本至今没见到影子，无奈只好自己动手了。
<!--more-->
### 环境
Ubuntu 14.04
Google Chrome 57.0.2987.110 (64-bit)
Python 2.7.6
Gedit
### 模拟登录
准备动手的时候想起来 lc 学长两年前写过一个查课脚本来着，就借了一份代码来学习一下。不过由于教务网登录的密码已经不是明文传输了（以前的教务网真是原始啊），所以需要改一改。
![](http://ooie9cjod.bkt.clouddn.com/17-5-6/40091671-file_1494000465676_b1fc.png)
F12 打开开发者工具，再打开[教务网页面](http://202.202.1.176:8080)，注意到 `index_login.aspx`。打开 http://202.202.1.176:8080/_data/index_login.aspx, 发现正是登录界面。
![](http://ooie9cjod.bkt.clouddn.com/17-5-6/78503950-file_1494000470056_13eab.png)
输入学号密码，登录之后发现马上跳转到了管理系统界面，这样就看不到提交的表单了。于是在回车登录之后立刻点击开发者工具栏的小红点，停止记录 network log, 终于得到表单啦：

![](http://ooie9cjod.bkt.clouddn.com/17-5-6/45029636-file_1494001185032_16a31.png)

```
# Form Data
__VIEWSTATE:*********
__VIEWSTATEGENERATOR:*********
Sel_Type:STU
txt_dsdsdsdjkjkjc:20144666
txt_dsdfdfgfouyy:
txt_ysdsdsdskgf:
pcInfo:
typeName:
aerererdsdxcxdfgfg:
efdfdfuuyyuuckjg:*********
```
显然 `Sel_Type` 是身份字段，`txt_dsdsdsdjkjkjc` 是学号。而`efdfdfuuyyuuckjg` 是加密后的密码。（后俩命名都是些什么鬼）经我验证其他字段都可以不填。

而一长串的 headers 经我验证也只需要一行 cookies. 再次感慨一下水水的教务网～
```
# Request Headers
Cookie:ASP.NET_SessionId=byhlubbmgt33bpjp3ve0avra; 
```
现在已经拿到了 cookies 和 表单，教务网也没有验证码，可以登录了。登录用到了 [`urllib`](https://docs.python.org/2/library/urllib.html) 和 [`urllib2`](https://docs.python.org/2/library/urllib2.html) 模块，可以查看文档。
```python
postdata = urllib.urlencode({  
        'Sel_Type':'STU',
        'txt_dsdsdsdjkjkjc':'20144666',
        'efdfdfuuyyuuckjg':'*********'
    })
    headers = {'Cookie':'ASP.NET_SessionId=byhlubbmgt33bpjp3ve0avra; ASPSESSIONIDQQTCCQAS=EGIHKHMDICJMHEJAEGKCEJHI'}
    req = urllib2.Request(  
        url = 'http://202.202.1.176:8080/_data/index_login.aspx',  
        data = postdata,
        headers = headers
    )
    res = opener.open(req)
```
登陆成功之后，还需要知道如何查询课表。
![](http://ooie9cjod.bkt.clouddn.com/17-5-6/58700645-file_1494000472790_c8f.png)
教学安排 -> 查看个人课表 -> 检索 -> 得到表单，再附上 cookies 就好啦～

`Sel_XNXQ` 显然是学年学期，然而我尝试了下 20151 这个属性发现并不行，可能非当前学期的课表都被吃了吧……

```
# Form Data
Sel_XNXQ:20161
rad:on
px:1
```
注意教务网用的是 GBK编码， 需要转码一下。
```python
    postdata = urllib.urlencode({  
        'Sel_XNXQ':'20161',
        'rad':'on',
        'px':'1'
    })
    headers = {'Cookie':'ASP.NET_SessionId=byhlubbmgt33bpjp3ve0avra; ASPSESSIONIDQQTCCQAS=EGIHKHMDICJMHEJAEGKCEJHI'}
    req = urllib2.Request(  
        url = 'http://202.202.1.176:8080/znpk/Pri_StuSel_rpt.aspx',  
        data = postdata,
        headers = headers
    )
    res = opener.open(req)
    str = res.read()
    return str.decode('gb2312').encode('utf-8')
```
另外，开放式实验课的课表需要去[实验教学网](http://syjx.cqu.edu.cn/)上查，好在实验教学网提供了一个公共的[查课接口](http://syjx.cqu.edu.cn/admin/query/student)，返回 JSON 数据，真是良心啊。这个 so easy, 我决定直接贴代码。
```python
def getExCourses():
    postdata = urllib.urlencode({
        'stuNum':'20144666'
    })
    req = urllib2.Request(url = 'http://syjx.cqu.edu.cn/admin/schedule/getStudentSchedule', data = postdata)
    res = opener.open(req)
    str = res.read()
    return str
```
这两次查询有重复结果，下一篇再来讨论如何解析数据并且去重好了。

### 题外话：教务网的密码加密
查看 [登录页面](http://202.202.1.176:8080/_data/index_login.aspx) 的源码，以 `efdfdfuuyyuuckjg`（即 post data 中的密码） 为关键词搜索，注意到 `chkpwd()` 函数：
```js
function chkpwd(obj)
            {
                var schoolcode="10611";
                var yhm=document.all.txt_dsdsdsdjkjkjc.value;
                if(obj.value!="")
                {
                    if(document.all.Sel_Type.value=="ADM")
                       yhm=yhm.toUpperCase();
                    var s=md5(yhm+md5(obj.value).substring(0,30).toUpperCase()+schoolcode).substring(0,30).toUpperCase();
                    document.all.efdfdfuuyyuuckjg.value=s;
                }
                else
                {
                    document.all.efdfdfuuyyuuckjg.value=obj.value;
                }
            }
```
这段代码显然是 md5 加密来着，大家都能看懂，就不具体解释了。

我准备抽空再研究一下配置文件怎么写，这样就可以优雅地把代码丢上 Github 了呢～也方便（未来有可能用我这个小轮子的）大家填写密码。

### 不是最后的最后
感谢提供旧版查课源代码的 lc 学长。

其实吧，写这个添课脚本花的时间肯定比 每次手动导入的时间 × 8（学期数） 要长多了，毕竟 Python 新手，有机会的话打算封装一下造福后人。

### 参考
https://wizardforcel.gitbooks.io/liaoxuefeng/py3/75.html
https://docs.python.org/2/library/urllib.html
https://docs.python.org/2/library/urllib2.html