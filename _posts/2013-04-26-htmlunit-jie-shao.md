---
layout: post
title: "HtmlUnit 介绍"
description: ""
category: WebDriver
tags: [test-china.org]
---


在 @Lily 的 [Juint4 + WebDriver 入门篇][1] 中， Lily 问起了 HtmlUnit Driver。对 HtmlUnit Driver 我倒是没有接触太多， 所以特意去找了些文章看看。

在 [selenium 官方 wiki][2] 上说：

> This is currently the fastest and most lightweight implementation of WebDriver. As the name suggests, this is based on HtmlUnit.

所以想要了解 HtmlUnit Driver， 就必先追根溯源地研究下 HtmlUnit。


> HtmlUnit is a "GUI-Less browser for Java programs". It models HTML documents and provides an API that allows you to invoke pages, fill out forms, click links, etc... just like you do in your "normal" browser.

简单来说， HtmlUnit 就是没有界面的，你看不见的，但是却能做浏览器的活的 JAVA 版本的浏览器。需要说明的是，**HtmlUnit 本身不是一个测试框架， 它真的只是一个模拟浏览器行为的程序。** 所以需要和 Junit 或者 TestNG 等测试框架配合起来使用。同时，很多开源的工具也使用 HtmlUnit 来模拟浏览器行为，比如大名鼎鼎的 WebDriver。

下面，搬抄一把 HtmlUnit 的入门指南：

## 建立项目 ##

使用 maven 创建一个 htmlunit 的试用项目

    mvn archetype:generate -DgroupId=com.testchina -DartifactId=my-htmlunit-test -DinteractiveMode=false

（不得不夸下 Maven， 构建的好工具啊。）

修改 pom.xml， 加入 HtmlUnit 依赖， 升级 Junit 为4.10。

```xml
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>

      <groupId>com.testchina</groupId>
      <artifactId>my-htmlunit-test</artifactId>
      <version>1.0-SNAPSHOT</version>
      <packaging>jar</packaging>

      <name>my-htmlunit-test</name>
      <url>http://maven.apache.org</url>

      <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
      </properties>

      <dependencies>
        <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <version>4.10</version>
          <scope>test</scope>
        </dependency>

        <dependency>
          <groupId>net.sourceforge.htmlunit</groupId>
          <artifactId>htmlunit</artifactId>
          <version>2.10</version>
        </dependency>

      </dependencies>
    </project>
```

然后，执行：

 1. `mvn install`
 2. `mvn eclipse:eclipse`

之后， 就可以在 eclipse 里导入项目。接着，我们来写第一个例子。

## 试用 ##

**第一个例子：**

a. 访问 http://htmlunit.sourceforge.net

b. 验证网页的 titile

c. 验证网页内容

```java
    package com.testchina;

    import static org.junit.Assert.*;
    import org.junit.Test;
    import com.gargoylesoftware.htmlunit.WebClient;
    import com.gargoylesoftware.htmlunit.html.HtmlPage;

    public class MyHtmlUnitTest {
        @Test
        public void homePage() throws Exception {
            final WebClient webClient = new WebClient(); // 建立 WebClient。
            final HtmlPage page = webClient.getPage("http://htmlunit.sourceforge.net"); // 访问网站，返回页面对象。
            assertEquals("HtmlUnit - Welcome to HtmlUnit", page.getTitleText()); // 验证 title。
            /** 验证网页内容 */
            final String pageAsXml = page.asXml();
            assertTrue(pageAsXml.contains("<body class=\"composite\">"));
            final String pageAsText = page.asText();
            assertTrue(pageAsText.contains("Support for the HTTP and HTTPS protocols"));
            webClient.closeAllWindows();
        }
    }
```

**第二个例子: 指定浏览器**

```java
    @Test
    public void homePage_Firefox() throws Exception {
        final WebClient webClient = new WebClient(BrowserVersion.FIREFOX_10);
        final HtmlPage page = webClient
                .getPage("http://htmlunit.sourceforge.net");
        assertEquals("HtmlUnit - Welcome to HtmlUnit", page.getTitleText());

        webClient.closeAllWindows();
    }
```

很简单，在初始化 WebClient 的时候，传入浏览器版本， 目前 2.10 里面支持：

```java
     /** Register plugins for the browser versions. */
        static {
            INTERNET_EXPLORER_6.initDefaultFeatures();
            INTERNET_EXPLORER_7.initDefaultFeatures();
            INTERNET_EXPLORER_8.initDefaultFeatures();

            FIREFOX_3.initDefaultFeatures();
            FIREFOX_3_6.initDefaultFeatures();
            FIREFOX_10.initDefaultFeatures();

            final PluginConfiguration flash = new PluginConfiguration("Shockwave Flash",
                "Shockwave Flash 9.0 r31", "libflashplayer.so");
            flash.getMimeTypes().add(new PluginConfiguration.MimeType("application/x-shockwave-flash",
                "Shockwave Flash", "swf"));
            FIREFOX_3.getPlugins().add(flash);
            FIREFOX_3_6.getPlugins().add(flash);
            FIREFOX_10.getPlugins().add(flash);

            CHROME_16.initDefaultFeatures();
            CHROME_16.setApplicationCodeName("Mozilla");
            CHROME_16.setPlatform("MacIntel");
            CHROME_16.setCpuClass(null);
            CHROME_16.setBrowserLanguage("undefined");
            // there are other issues with Chrome; a different productSub, etc.
        }
```

**第三个例子： 获取页面元素**

```java
    @Test
    public void getElements() throws Exception {
        final WebClient webClient = new WebClient();
        final HtmlPage page = webClient.getPage("http://htmlunit.sourceforge.net");
        //get list of all divs
        final List<?> divs = page.getByXPath("//div"); // 返回所有的div
        System.out.println(((HtmlDivision)divs.get(0)).asText()); // 得到第一个 Div 节点的内容。

        /** 返回所有的超链接， 并打印 name 属性*/
        final List<HtmlAnchor> anchors = page.getAnchors();
        for (HtmlAnchor anchor : anchors) {
            System.out.println(anchor.getAttribute("name"));
        }
        webClient.closeAllWindows();
    }
```

**第四个例子： 提交表单**

```java
    @Test
    public void submittingForm() throws Exception {
        final WebClient webClient = new WebClient();

        // Get the first page
        final HtmlPage page1 = webClient.getPage("http://some_url");

        // Get the form that we are dealing with and within that form,
        // find the submit button and the field that we want to change.
        final HtmlForm form = page1.getFormByName("myform");

        final HtmlSubmitInput button = form.getInputByName("submitbutton");
        final HtmlTextInput textField = form.getInputByName("userid");

        // Change the value of the text field
        textField.setValueAttribute("root");

        // Now submit the form by clicking the button and get back the second page.
        final HtmlPage page2 = button.click();

        webClient.closeAllWindows();
    }
```

----------


是不是很棒？ 所有浏览器能做的，它都能做，我们再来看下它的特性：

 - 支持 HTTP and HTTPS 协议
 - 支持 cookies
 - 支持 Post， Get， Head， Delete
 - 支持代理
 - 能分析 HTTP responses
 - 能自定义 HTTP request head
 - 支持 Javascript

于是有
**第五个例子： 使用 javascript**

这里的 Javascript 的意思，是指 HtmlUnit 可以通过 js 引擎执行 javascript 代码。

> HtmlUnit provides excellent JavaScript support, simulating the behavior of the configured browser (Firefox or Internet Explorer). It uses the Rhino JavaScript engine for the core language (plus workarounds for some Rhino bugs) and provides the implementation for the objects specific to execution in a browser.

比如我们的页面有一个 alert 事件：

```html
    <html><head><title>Alert sample</title></head>
    <body onload='alert("foo");'>
    </body></html>
```

我们如何捕捉它呢？

```java
    @Test
    public void alerts() throws Exception {
        final WebClient webClient = new WebClient();

        final List collectedAlerts = new ArrayList();
        webClient.setAlertHandler(new CollectingAlertHandler(collectedAlerts));

        // Since we aren't actually manipulating the page, we don't assign
        // it to a variable - it's enough to know that it loaded.
        webClient.getPage("http://tciludev01/test.html");

        final List expectedAlerts = Collections.singletonList("foo");
        assertEquals(expectedAlerts, collectedAlerts);
    }
```

是不是很轻巧？ 像真正的浏览器一样， HtmlUnit 可以响应各种 Javascript 事件， 执行各种 Javascript 代码。


----------


对于 HtmlUnit， 我就初略看了看， 总的印象是轻，不用启动笨重的浏览器就能做到和浏览器一样的行为， 和 Junit4 很好的搭配，可以
写出轻巧优美的测试案例， 同时也能为测试省去不少时间。这在前期测试驱动开发的时候，特别是 Web 应用开发，可以节省去不少时间。

参见:

http://htmlunit.sourceforge.net/

http://maven.apache.org/guides/getting-started/maven-in-five-minutes.html

http://code.google.com/p/selenium/wiki/HtmlUnitDriver

http://htmlunit.sourceforge.net/javascript-howto.html

  [1]: http://test-china.org/topics/8
  [2]: http://code.google.com/p/selenium/wiki/HtmlUnitDriver
