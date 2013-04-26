---
layout: post
title: "webdriver 之测试失败自动截图"
category: WebDriver
tags: [test-china.org]
---

写 webDriver 测试的时候最头疼的就是调试。 但也远不及运行的时候出错，再回头调试来的痛苦。总所周知， web 自动化的代码都非常脆弱，一份代码一会运行失败，一会运行成功也是很正常的事情。总的来说造成案例运行失败的原因大抵有两点：

 - 环境问题： 比如网络不稳定啊
 - 代码变动： 比如某个元素不在
 - 遇到 bug ：这就是真的发现 bug 了

无论哪一种，遇到了都需要花一番时间去 debug。那如果这个时候有一张运行时候出错的截图，那就一目了然了。（即便不一目了然，也有很多帮助）

在运行出错的时候，捕获错误并截图有两种思路：

 1. 自定义一个 WeDdriver 的监听器，在出异常的时候截图。
 2. 利用 Juint 的 TestRule， 自定义一个 Rule 在运行失败的时候截图。





自定义监听器
-------


----------

**截图的原理**

截图需要用到 RemoteWebDriver。在 [Selenium][1] 官方我们可以找到：

> One nice feature of the remote
> webdriver is that exceptions often
> have an attached screen shot, encoded
> as a Base64 PNG. In order to get this
> screenshot, you need to write code
> similar to:

    public String extractScreenShot(WebDriverException e) {
      Throwable cause = e.getCause();
      if (cause instanceof ScreenshotException) {
        return ((ScreenshotException) cause).getBase64EncodedScreenshot();
      }
      return null;
    }

  [1]: http://code.google.com/p/selenium/wiki/RemoteWebDriver

意思就是说，每个异常都是 ScreenshotException 的对象，转码一下就可以用了。这是截图的本质。

```java
    import java.io.File;
    import java.io.FileOutputStream;
    import java.io.IOException;
    import java.text.SimpleDateFormat;
    import java.util.Date;

    import org.openqa.selenium.WebDriver;
    import org.openqa.selenium.internal.Base64Encoder;
    import org.openqa.selenium.remote.ScreenshotException;
    import org.openqa.selenium.support.events.AbstractWebDriverEventListener;

    /**
     * This is an customized webdriver event listener.
     * Now it implements onException method: webdriver will take a screenshot
     * when it meets an exception. It's good but not so usable. And when we use
     * WebDriverWait to wait for an element appearing, the webdriver will throw
     * exception always and the onException will be excuted again and again, which
     * generates a lot of screenshots.
     * Put here for study
     * Usage:
     *      WebDriver driver = new FirefoxDriver();
     *      WebDriverEventListener listener = new CustomWebDriverEventListener();
     *      return new EventFiringWebDriver(driver).register(listener);
     *
     * @author qa
     *
     */
    public class CustomWebDriverEventListener extends
            AbstractWebDriverEventListener {

        @Override
        public void onException(Throwable throwable, WebDriver driver) {

            Throwable cause = throwable.getCause();
            if (cause instanceof ScreenshotException) {
                SimpleDateFormat formatter = new SimpleDateFormat(
                        "yyyy-MM-dd-hh-mm-ss");
                String dateString = formatter.format(new Date());
                File of = new File(dateString + "-exception.png");
                FileOutputStream out = null;
                try {
                    out = new FileOutputStream(of);
                    out.write(new Base64Encoder()
                            .decode(((ScreenshotException) cause)
                                    .getBase64EncodedScreenshot()));
                }
                catch (Exception e) {
                    e.printStackTrace();
                }
                finally {
                    try {
                        out.close();
                    }
                    catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
        }

    }
```

主要看 onException 这个方法的实现，很明显， 我们捕获了这个异常， 然后通过强制转换将图片提取出来，写入硬盘。

然后就是使用这个监听器， 通常会在 setup 方法里面将这个监听器注册到 WebDriver 中去， 看代码：

```java
    @Test
    public void setup(){
        String remote_driver_url = "http://localhost:4444/wd/hub";
        DesiredCapabilities capability = null;
        capability = DesiredCapabilities.firefox();
        WebDriverEventListener eventListener = new CustomWebDriverEventListener ();
        WebDriver driver = new EventFiringWebDriver(new RemoteWebDriver(new URL(
                        remote_driver_url), capability)).register(eventListener);
    }
```

在这之后，如果运行出错， WebDriver 抛出异常就会在相应的 classpath 下面生成 png 的截图。

自定义 TestRule
-------


----------

和自定义 WebDriver 监听器不同， 自定义 TestRule 只有在这个 Rule 被执行的时候， 才去做一些我们预设的 CallBack。 所以这个截图动作，对于 WebDriver 而言， 是主动的。 那么，我们就需要自定义一个 RemoteWebDriver 来实现截图功能。 WebDriver 自身提供了 TakesScreenshot  这个接口， 我们只要实现它就可以了， 看代码：

```java
    import java.net.URL;

    import org.openqa.selenium.OutputType;
    import org.openqa.selenium.TakesScreenshot;
    import org.openqa.selenium.WebDriverException;
    import org.openqa.selenium.remote.CapabilityType;
    import org.openqa.selenium.remote.DesiredCapabilities;
    import org.openqa.selenium.remote.DriverCommand;
    import org.openqa.selenium.remote.RemoteWebDriver;

    public class CustomRemoteWebDriver extends RemoteWebDriver implements
            TakesScreenshot {
        public CustomRemoteWebDriver(URL url, DesiredCapabilities dc) {
            super(url, dc);
        }

        @Override
        public <X> X getScreenshotAs(OutputType<X> target)
                throws WebDriverException {
            if ((Boolean) getCapabilities().getCapability(
                    CapabilityType.TAKES_SCREENSHOT)) {
                return target
                        .convertFromBase64Png(execute(DriverCommand.SCREENSHOT)
                                .getValue().toString());
            }
            return null;
        }
    }
```

然后，我们在加一个封装类， 将截图方法放进去。

 WebDriverWrapper.screenShot ：


```java
     /**
         * Function to take the screen shot and save it to the classpath dir.
         * Usually, you will find the png file under the project root.
         *
         * @param driver
         *            Webdriver instance
         * @param desc
         *            The description of the png
         */
        public static void screenShot(WebDriver driver, String desc) {
            Date currentTime = new Date();
            SimpleDateFormat formatter = new SimpleDateFormat("yyyy-MM-dd-hh-mm-ss");
            String dateString = formatter.format(currentTime);
            File scrFile = ((TakesScreenshot) driver)
                    .getScreenshotAs(OutputType.FILE);
            try {
                desc = desc.trim().equals("") ? "" : "-" + desc.trim();
                File screenshot = new File("screenshot" + File.separator
                        + dateString + desc + ".png");
                FileUtils.copyFile(scrFile, screenshot);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
```


下面，就是添加 Junit 的 TestRule：

```java
    import org.junit.rules.TestRule;
    import org.junit.runner.Description;
    import org.junit.runners.model.Statement;
    import org.openqa.selenium.WebDriver;

    public class TakeScreenshotOnFailureRule implements TestRule {

        private final WebDriver driver;

        public TakeScreenshotOnFailureRule(WebDriver driver) {
            this.driver = driver;
        }

        @Override
        public Statement apply(final Statement base, Description description) {
            return new Statement() {
                @Override
                public void evaluate() throws Throwable {
                    try {
                        base.evaluate();
                    }
                    catch (Throwable throwable) {
                        WebDriverWrapper.screenShot(driver, "assert-fail");
                        throw throwable;
                    }
                }
            };
        }
    }
```

代码很简单，在抛出 evalate 方法的错误之前，截图。




然后就是使用这个 TestRule， 很简单，只要在你的 测试用例里面加入：

```java
    public class MyTest {
    ...
    @Rule
    public TestRule myScreenshot = new TakeScreenshotOnFailureRule(driver);
    ...
    @Test
    public void test1() {}

    @Test
    public void test2() {}
    ...
    }
```

即可。关于 Junit 的 Rule 请自行 google！

两则的比较
-----


----------

总得来说，两种方法都很方便， 也很有效果， 基本都能截图成功。

不同之处在于，

 - RemoteWebDriver 监听器是在 RemoteWebDriver 抛出异常的时候截图。
 - TestRule 是在 assert 失败的时候截图。

我在项目中最早是用第一种方法，后来改用第二种，主要是因为，在自定义的监听器里， 它遇到所有的异常都会截图，这个时候，如果你用了 condition wait 一个 Ajax 的元素， 那就会很悲剧，你会发现在你的目录下面有无数的截图。当初我没有找到解决方法，期待有人提出。


## Refer ##


----------


 - http://code.google.com/p/selenium/wiki/RemoteWebDriver
 - http://magustest.com/blog/webdriver/webdriver-screenshot-on-exception/
 - http://cwd.dhemery.com/2010/12/junit-rules/
