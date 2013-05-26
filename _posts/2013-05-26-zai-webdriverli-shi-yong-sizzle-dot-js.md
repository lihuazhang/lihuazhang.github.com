---
layout: post
title: "在WebDriver里使用Sizzle.js"
description: ""
categories: WebDriver
tags: []
---

##XPATH vs CSS

2008年的时候， 开始使用 Selenium RC 来写 Web 自动化测试，用的是 XPATH 定位，在 Firefox 上工作的很好，可惜在 IE 上跑不起来。
因为后来转去做开发工作，对此问题也没深究。时隔5年，09年时候转测试，已经4年多了。Selenium RC 都变成了 WebDriver， 从2013 GTAC 来看，
WebDriver 逐渐成为 Web 自动化的主流框架。

WebDriver 同样使用 XPATH 和 CSS locator。孰优孰劣，google 一下，一大把，不过目前风向偏向 CSS， 理由如下

1. 更快(尤其在 IE 上)
1. 更简单
2. 更加可读
3. 符合 Jquery 定位逻辑
4. XPATH 在不同浏览器上的实现可能不太一样，也许在 Firefox 工作，在 IE 上就不一定了。
5. CSS 可以引入第三方的 Javascript 库来更好的定位元素， 比如马上要说的 Sizzle.js

个人一直使用 XPATH， 对 XPATH 的强大也深有体会。作为一个软件测试人员，坚持一点`什么好用，用什么`，
所以想体会一把 CSS locator， 至于争论，请移步 [using xpath is better of CSS selector?](https://groups.google.com/forum/?fromgroups#!topic/webdriver/raDyUewHFGI)

##CSS

在 Selenium 1 的时代， CSS 定位是用 Sizzle 库实现的，但是到了 Selenium 2 时代，webdriver 首先考虑native的实现，只有当native 不存在的时候，才会
去使用Sizzle。

> Native browser support is used by default, so please refer to w3c css selectors <http://www.w3.org/TR/CSS/#selectors> for a list of generally available css selectors. If a browser does not have native support for css queries, then Sizzle is used. 
 
