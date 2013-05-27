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

WebDriver 中有 8 种定位方法，


1. By.className
2. By.id
3. By.linkText
4. By.name
5. By.partialLinkText
6. By.tagName
7. By.xpath
8. By.cssSelector

其他 6 种其实都是由 XPATH 和 CssSelector 来实现的。参考[Selecting WebDriver locators](http://darrellgrainger.blogspot.com/2011/12/selecting-webdriver-locators.html)


<table  class="table">
   <tr>
      <td>Original</td>
      <td>CSS</td>
      <td>XPath</td>
   </tr>
   <tr>
      <td>By.className("foo");</td>
      <td>By.cssSelector(".foo");</td>
      <td>By.xpath("//*[@class='foo']");</td>
   </tr>
   <tr>
      <td>By.id("bar");</td>
      <td>By.cssSelector("#bar");</td>
      <td>By.xpath("//*[@id='bar']");</td>
   </tr>
   <tr>
      <td>By.linkText("Click Me");</td>
      <td>N/A (可以利用sizzle :contains(text) 实现 )</td>
      <td>By.xpath("//a[text()='Click Me']");</td>
   </tr>
   <tr>
      <td>By.name("fee");</td>
      <td>By.cssSelector("[name='fee']");</td>
      <td>By.xpath("//*[@name='fee']");</td>
   </tr>
   <tr>
      <td>By.partialLinkText("some");</td>
      <td>N/A (可以利用sizzle :contains(text) 实现 )</td>
      <td>By.xpath("//a[contains(text(),'some')]");</td>
   </tr>
   <tr>
      <td>By.tagname("div");</td>
      <td>By.cssSelector("div");</td>
      <td>By.xpath("//div");</td>
   </tr>
</table>



其实在 Selenium 1 的时代， CSS 定位是用 Sizzle 库实现的，但是到了 Selenium 2 时代，webdriver 首先考虑native的实现，只有当native 不存在的时候，才会
去使用Sizzle。

> Native browser support is used by default, so please refer to w3c css selectors <http://www.w3.org/TR/CSS/#selectors> for a list of generally available css selectors. If a browser does not have native support for css queries, then Sizzle is used. 
 
按道理来说，既然都用 Sizzle 做了备选，应该不用人为植入 Sizzle 支持，但是由于各种 driver 的实现不同，总会出现一些不一致，所以不如主动来的省心。


##Sizzle

>A pure-JavaScript CSS selector engine designed to be easily dropped in to a host library.

做为 JQuery 的定位器，Sizzle 号称最快的 DOM 选择器。它可以和 JQuery 结合， 也可以单独使用。

Sizzle 支持:

1. IE6+
2. FF3.0+
3. Chrome 5+
4. Safari 3+
5. Opera 9+

详细可参见[文档](https://github.com/jquery/sizzle/wiki/Sizzle-Documentation)


##方案

网上的方案大抵有 2 个:

### 1. 自定义一个 Selector 类，封装 Sizzle.js 脚本。

`SizzleSelector.java`

```java

package my.webdriver.helper;

import java.util.List;
import org.openqa.selenium.JavascriptExecutor;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebDriverException;
import org.openqa.selenium.WebElement;
 
@SuppressWarnings("unchecked")
public class SizzleSelector {
    private JavascriptExecutor driver;
 
    public SizzleSelector(WebDriver webDriver) {
        driver = (JavascriptExecutor) webDriver;
    }
 
    public WebElement findElementBySizzleCss(String using) {
        injectSizzleIfNeeded();
        String javascriptExpression = createSizzleSelectorExpression(using);
        List<WebElement> elements = (List<WebElement>) driver
                .executeScript(javascriptExpression);
        if (elements.size() > 0)
            return (WebElement) elements.get(0);
        return null;
    }
 
    public List<WebElement> findElementsBySizzleCss(String using) {
        injectSizzleIfNeeded();
        String javascriptExpression = createSizzleSelectorExpression(using);
        return (List<WebElement>) driver.executeScript(javascriptExpression);
    }
 
    private String createSizzleSelectorExpression(String using) {
        return "return Sizzle(\"" + using + "\")";
    }
 
    private void injectSizzleIfNeeded() {
        if (!sizzleLoaded())
            injectSizzle();
    }
 
    public Boolean sizzleLoaded() {
        Boolean loaded;
        try {
            loaded = (Boolean) driver.executeScript("return Sizzle()!=null");
        } catch (WebDriverException e) {
            loaded = false;
        }
        return loaded;
    }
 
    public void injectSizzle() {
        driver.executeScript(" var headID = document.getElementsByTagName(\"head\")[0];"
                + "var newScript = document.createElement('script');"
                + "newScript.type = 'text/javascript';"
                + "newScript.src = 'https://raw.github.com/jquery/sizzle/master/sizzle.js';"
                + "headID.appendChild(newScript);");
    }
}

```
在需要使用 SizzleSelector 的时候，使用它。

```java

package my.webdriver.pageobject;

import my.webdriver.helper.SizzleSelector;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;

public class SearchResultPage {

    private final WebDriver driver;
    private SizzleSelector sizzler;

    public SearchResultPage(WebDriver driver) {
        this.driver = driver;
        sizzler = new SizzleSelector(driver);
    }

    By resultStats = By.id("resultStats");

    public String getResultStats() {
        // 下面两个输出的结果应该一样
        System.out.println(sizzler.findElementsBySizzleCss("a:contains(翻译此页)").size());
        System.out.println(driver.findElements(By.xpath("//a[contains(text(), '翻译此页')]")).size());
       
        return driver.findElement(resultStats).getText();
    }

}

```

### 2. 扩展 By 和 ByCssSelector 类

```java

package my.webdriver.helper;

import java.lang.reflect.Method;
import java.util.List;

import org.openqa.selenium.By;
import org.openqa.selenium.InvalidElementStateException;
import org.openqa.selenium.JavascriptExecutor;
import org.openqa.selenium.SearchContext;
import org.openqa.selenium.WebDriverException;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.internal.FindsByCssSelector;
import org.openqa.selenium.remote.RemoteWebElement;

public abstract class ByExtended extends By {

    
    public static By cssSelector(final String selector) {
        if (selector == null)
            throw new IllegalArgumentException(
                    "Cannot find elements when the selector is null");

        return new ByCssSelectorExtended(selector);
    }
    

    public static class ByCssSelectorExtended extends ByCssSelector {

        private String ownSelector;

        public ByCssSelectorExtended(String selector) {
            super(selector);
            ownSelector = selector;
        }

        @Override
        public WebElement findElement(SearchContext context) {
            try {
                if (context instanceof FindsByCssSelector) {
                    return ((FindsByCssSelector) context)
                            .findElementByCssSelector(ownSelector);
                }
            } catch (InvalidElementStateException e) {
                return findElementBySizzleCss(context, ownSelector);
            } catch (WebDriverException e) {
                if (e.getMessage().startsWith(
                        "An invalid or illegal string was specified")) {
                    return findElementBySizzleCss(context, ownSelector);
                }
                throw e;
            }
            throw new WebDriverException("Driver does not support finding an element by selector: " + ownSelector);
        }

        @Override
        public List<WebElement> findElements(SearchContext context) {
            try {
                if (context instanceof FindsByCssSelector) {
                    return ((FindsByCssSelector) context)
                            .findElementsByCssSelector(ownSelector);
                }
            } catch (InvalidElementStateException e) {
                return findElementsBySizzleCss(context, ownSelector);
            } catch (WebDriverException e) {
                if (e.getMessage().startsWith(
                        "An invalid or illegal string was specified")) {
                    return findElementsBySizzleCss(context, ownSelector);
                }
                throw e;
            }
            throw new WebDriverException("Driver does not support finding an element by selector: " + ownSelector);
        }

        @Override
        public String toString() {
            return "ByExtended.selector: " + ownSelector;
        }


         /********************************* SIZZLE SUPPORT CODE**************************************/
    

        /**
         * Find element by sizzle css.
         * @param context 
         * 
         * @param cssLocator
         *            the cssLocator
         * @return the web element
         */
        @SuppressWarnings("unchecked")
        public WebElement findElementBySizzleCss(SearchContext context, String cssLocator) {
            List<WebElement> elements = findElementsBySizzleCss(context, cssLocator);
            if (elements != null && elements.size() > 0 ) {
                return elements.get(0);
            }
            return null;
        }

        private void fixLocator(SearchContext context, String cssLocator,
                WebElement element) {

            if (element instanceof RemoteWebElement) {
                try {
                    Class[] parameterTypes = new Class[] { SearchContext.class,
                            String.class, String.class };
                    Method m = element.getClass().getDeclaredMethod(
                            "setFoundBy", parameterTypes);
                    m.setAccessible(true);
                    Object[] parameters = new Object[] { context,
                            "css selector", cssLocator };
                    m.invoke(element, parameters);
                } catch (Exception fail) {
                    //NOOP Would like to log here? 
                }
            }
        }


        /**
         * Find elements by sizzle css.
         * 
         * @param cssLocator
         *            the cssLocator
         * @return the list of the web elements that match this locator
         */
        @SuppressWarnings("unchecked")
        public List<WebElement> findElementsBySizzleCss(SearchContext context, String cssLocator) {
            injectSizzleIfNeeded();
            String javascriptExpression = createSizzleSelectorExpression(cssLocator);
            List<WebElement> elements = (List<WebElement>) ((JavascriptExecutor) getDriver())
                    .executeScript(javascriptExpression);
            if (elements.size() > 0) {
                for (WebElement el : elements) { 
                    fixLocator(context, cssLocator, el);
                }
            }
            return elements;
        }

        private JavascriptExecutor getDriver() {
            
            // 从上下文中取出当前的 driver
            return null;
        }

        /**
         * Creates the sizzle selector expression.
         * 
         * @param cssLocator
         *            the cssLocator
         * @return string that represents the sizzle selector expression.
         */
        private String createSizzleSelectorExpression(String cssLocator) {
            return "return Sizzle(\"" + cssLocator + "\")";
        }

        /**
         * Inject sizzle if needed.
         */
        private void injectSizzleIfNeeded() {
            if (!sizzleLoaded())
                injectSizzle();
        }

        /**
         * Check if the Sizzle library is loaded.
         * 
         * @return the true if Sizzle is loaded in the web page 
         */
        public Boolean sizzleLoaded() {
            Boolean loaded;
            try {
                loaded = (Boolean) ((JavascriptExecutor) getDriver())
                        .executeScript("return Sizzle()!=null");
            } catch (WebDriverException e) {
                loaded = false;
            }
            return loaded;
        }

        /**
         * Inject sizzle 1.8.2
         */
        public void injectSizzle() {
            ((JavascriptExecutor) getDriver())
                    .executeScript(" var headID = document.getElementsByTagName(\"head\")[0];"
                            + "var newScript = document.createElement('script');"
                            + "newScript.type = 'text/javascript';"
                            + "newScript.src = 'https://raw.github.com/jquery/sizzle/1.8.2/sizzle.js';"
                            + "headID.appendChild(newScript);");
        }
        /**
         * ******************** SIZZLE SUPPORT CODE
         */

    }
}

```
