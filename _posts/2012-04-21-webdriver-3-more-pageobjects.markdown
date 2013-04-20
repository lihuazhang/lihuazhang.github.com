---
layout: post
title: "WebDriver 3: more PageObjects - PageFactory"
date: 2012-04-21 10:36
comments: true
categories: WebDriver 
---

在上篇中，大致介绍了PageObjects。其实为了对PageObjects更好的支持，webdriver中还有一个
[PageFactory](http://code.google.com/p/selenium/wiki/PageFactory)的概念。

>There is a PageFactory in the support package that provides support for this pattern, and helps to remove some boiler-plate code from your Page Objects at the same time.

来看下,上篇中的百度搜索例子, 用PageFactory该如何实现。

* 首先修改BaiduIndexPage

```java
package com.ahchoo.automation.page;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;
import org.openqa.selenium.support.How;

public class BaiduIndexPage {

    private WebDriver driver;
    private final String url = "http://www.baidu.com";

    public BaiduIndexPage(WebDriver driver) {
        this.driver = driver;
        driver.get(url);
    }

    @FindBy(how = How.ID, using = "kw")
    private WebElement searchField;

    public SearchResultPage searchFor(String text) {
        // We continue using the element just as before
        searchField.sendKeys(text);
        searchField.submit();
        return new SearchResultPage(driver);
    }
}
```

可以看到这里使用了注释@FindBy， 这是一个注释类，我们来看下它的定义

```java
public @interface FindBy {
  How how() default How.ID;

  String using() default "";

  String id() default "";

  String name() default "";

  String className() default "";

  String css() default "";

  String tagName() default "";

  String linkText() default "";

  String partialLinkText() default "";

  String xpath() default "";
}
```

我们可以知道其实FindBy注释就是实现了`driver.findElement(By)`,用FindBy可以从无数findElement方法中解脱出来(如果你页面有很多元素的时候)。

* 然后要用工厂模式生成BaiduIndexPage对象

在SearchTest.java 里
``` java
    BaiduIndexPage home = PageFactory.initElements(driver, BaiduIndexPage.class);
    // BaiduIndexPage home = new BaiduIndexPage(driver);
```
将WebDriver实例和BaiduIndexPage.class传给initElements方法，该方法会更具反射生成BaiduIndexPage实例，必须注意的是，使用PageFactory，我们的页面对象必须有
一个带有WebDriver类型参数的构造方法。


>This method will attempt to instantiate the class given to it, preferably using a constructor
>which takes a WebDriver instance as its only argument or falling back on a no-arg constructor.

PageFactory基本需要关注的就是：
    1. Findby注释
    2. PageFactory.initElements

我们继续改造，

```java
package com.ahchoo.automation.page;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;
import org.openqa.selenium.support.How;
import org.openqa.selenium.support.PageFactory;

public class BaiduIndexPage {

    private WebDriver driver;
    private final String url = "http://www.baidu.com";

    public BaiduIndexPage(WebDriver driver) {
        this.driver = driver;
        driver.get(url);
        PageFactory.initElements(driver, this);
    }

    @FindBy(how = How.ID, using = "kw")
    private WebElement searchField;

    @FindBy(how = How.ID, using = "lg")
    private WebElement logo;
    
    @FindBy(how = How.CLASS_NAME, using = "s_btn")
    private WebElement baidYiXiauButton;
    
    public SearchResultPage searchFor(String text) {
        // We continue using the element just as before
        searchField.sendKeys(text);
        searchField.submit();
        return new SearchResultPage(driver);
    }
    
    public boolean idLogoDisplay() {
        return logo.isDisplayed();
    }
    
    public boolean hasBaidYiXiauButton() {
        return baidYiXiauButton.isDisplayed();
    }
    
}
```

我们将`PageFactory.initElements(driver, this);`放在页面类的构造函数里，这样就不需要将PageFactory暴露到TestCase中去。

```java

package com.ahchoo.automation.page;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;
import org.openqa.selenium.support.How;
import org.openqa.selenium.support.PageFactory;

public class SearchResultPage {

    private WebDriver driver;
    
    /* 推广链接所在位置 */
    @FindBy(how=How.ID, using="ec_im_container")
    private WebElement adDiv;
    
    public SearchResultPage(WebDriver driver) {
        this.driver = driver;
        PageFactory.initElements(driver, this);
    }

    public String getTitle() {
        return driver.getTitle();
    }
    
    public String getContent() {
        return driver.getPageSource();
    }
    
    public boolean isAdDivDisplayed() {
        return adDiv.isDisplayed();
    }
}
```

下面是测试用例

```java

package com.ahchoo.automation;

import static org.junit.Assert.assertTrue;

import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.support.PageFactory;

import com.ahchoo.automation.page.BaiduIndexPage;
import com.ahchoo.automation.page.SearchResultPage;

/**
 * Unit test for simple App.
 */
public class SearchTest {
    private WebDriver driver;

    @Before
    public void setUp() {
        driver = new FirefoxDriver();
    }

    @After
    public void tearDown() {
        driver.close();
    }

    @Test
    public void searchTest() {
        BaiduIndexPage home = new BaiduIndexPage(driver);
        assertTrue(home.hasBaidYiXiauButton());
        assertTrue(home.idLogoDisplay());
        SearchResultPage searchResult = home.searchFor("pizza");
        assertTrue(searchResult.isAdDivDisplayed());
        assertTrue(searchResult.getTitle().contains("pizza"));
        assertTrue(searchResult.getContent().contains("pizza"));
    }
}
```

改造完毕，我可以保证这个Testcase能运行的很欢。

######总结
可以看到使用PageFactory可以使页面类代码更加清晰，维护也相对简单许多。如果你也使用PageObjects的话，
不妨试试看。如果你想多了解PageFactory的话，请移步[PageFactory](http://code.google.com/p/selenium/wiki/PageFactor)，
一定能让你受益匪浅。

