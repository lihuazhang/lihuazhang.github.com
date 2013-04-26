---
layout: post
title: "JMeter 兵器谱之 ForEach Controller"
category: Jmeter
tags: [test-china.org]
---

同样的，英文好的同学请移驾[ForEach Controller][1]

ForEach controller 是用来遍历集合的，比如 XPATH Extractor 或者 正则表达式 Extractor 解析出来的集合。ForEach做的事情大致有2件，

 1. 取得集合
 2. 遍历集合，将每一个值应用到 ForEach 下面的 Smapler 或者 controller 中去。

来看一个例子，我们要访问百度首页，取得首页所有的链接，然后访问这些链接。我们要做的是：

 1. 用一个 http sample 访问 http://www.baidu.com
 2. 在这个 sample 下添加一个正则表达式提取器，使用 &lt;a href="([^"]+)" 提取所有的 link 的 href 值。
 3. 用 ForEach Controller 遍历第2步提取出来的 href 值，然后一一访问。

JMeter 脚本结构大致如下，

![alt text][2]


先来看正则表达式提取器


![alt text][3]


注意这个引用名称 **inputVar**， 提取出来的所有匹配值都会放在这个变量里，事实上 **inputVar** 相当于一个 set 了。然后我们在紧跟的 ForEach 里面使用这个变量。

![alt text][4]

可以看到，在 ForEach 里，我们将之前的 **inputVar** 设置为输入变量。然后用 **returnVar** 做为输出。 这其实就类似于 JAVA 里面的 ForEach。

    for(Object returnVar : inputVar) {
    //process
    }

这个 process 过程就是下图所示：
![alt text][5]




  [1]: http://jmeter.apache.org/usermanual/component_reference.html#ForEach_Controller
  [2]: http://jmeter.apache.org/images/screenshots/logic-controller/foreach-example.png
  [3]: https://ers1ja.bn1.livefilestore.com/y1pDLRMxvFylmW1qmUQUvGe1DJVuwh_KUusRReatQ3QtB4ZxbWAN1HCxgqqZJnl6b9yv1rFXc8mTxe5_1LS4ji1zK4fhsRC6DWr/regular.png?psid=1
  [4]: https://ers1ja.bn1.livefilestore.com/y1p1K2BOBifU6X2D6r3Ij1cXIrT_D3mwqLArS24AiUmyiIzRzAc6vkXpldU7lxAkQhCWiMmqOoV07DiBMIxJKLyDa4vpJ72qPBp/foreach.png?psid=1
  [5]: https://ers1ja.bn1.livefilestore.com/y1pf_N1kVq04KkJwyA20UlawUiT_AmrnaudC6MyqCYrcnRyoer1rJXJEZM_lYgONZgC2IwH99dywgmKiVaFSUkXS3ktzbtHRZZ_/http%201.png?psid=1
