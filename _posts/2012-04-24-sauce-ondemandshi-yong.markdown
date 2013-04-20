---
layout: post
title: "Sauce OnDemand试用"
date: 2012-04-24 21:37
comments: true
categories: Sauce
---

闲着无聊，试用了下[saucelabs](https://saucelabs.com)。
先注册用户，这个略过不说。

saucelabs有两种方式:

1. Sauce Scout

    通过访问架在云端的浏览器(cloud-hosted browsers)执行测试用例。相当于saucelabs给用户提供了多种多样组合的虚拟机，只不过这个虚拟机就只有一个浏览器。如图：
    ![sourcescout](/photos/soucescout.png "soucescout")
    
    Saucelabs提供了许多方案，对于那些没有空间部署环境的公司，这个服务的确很贴心。
2. Sauce OnDemand
    
    Sauce OnDemand就是所谓的自动化了。我们利用WebDriver或者Selenium RC的remote server来指定SauceLabs提供的浏览器配置，进行各种自动化测试。

那我们开始试用Sauce OnDemand，英语好的攻城狮们请直接移步[Get Started](https://saucelabs.com/docs/ondemand/getting-started/env/ruby/se2/linux)


为了方便起见，我就用ruby。首先确保你安装了`selenium-webdriver`，如果没有安装，在终端用以下命令安装：
```
    gem install selenium-webdriver
```

输入以下代码：注意: 代码中的:url请替换成你自己的，不过我不知道这个url到底是哪里来的，
我是从get started里面的例子里找到的。

```ruby 
require 'rubygems'
require "test/unit"
require 'selenium-webdriver'

class ExampleTest < Test::Unit::TestCase
    def setup
        caps = Selenium::WebDriver::Remote::Capabilities.firefox
        caps.version = "5"
        caps.platform = :XP
        caps[:name] = "Testing Selenium 2 with Ruby on Sauce"

        @driver = Selenium::WebDriver.for(
          :remote,
          :url => "http://lihuazhang:ba444e83-206c-4a9c-b008-4da920d7f852@ondemand.saucelabs.com:80/wd/hub",
          :desired_capabilities => caps)
    end

    def test_sauce
        @driver.navigate.to "http://saucelabs.com/test/guinea-pig"
        assert @driver.title.include?("I am a page title - Sauce Labs")
    end

    def teardown
        @driver.quit
    end
end
```
运行`ruby basic-example.rb`

```
Run options: 

# Running tests:

.

Finished tests in 44.647462s, 0.0224 tests/s, 0.0224 assertions/s.

1 tests, 1 assertions, 0 failures, 0 errors, 0 skips

```

可以看到运行成功，我们再去sauce的[Account](https://saucelabs.com/account)页面里去看下运行的结果。

![account](/photos/souceaccount.png)

可以看到页面里有video和log下载，打开log我们可以看到运行的录像，而log就记录了运行的信息。
此外，我们更可以点开测试用例，查看更多的细节。

但是一般我们测试环境都是内网，为了满足这个条件，Saucelabs提供一条通道叫Sauce Connect。
> Sauce Connect creates a secure and reliable tunnel from our cloud to your private network that can only be accessed by you. This eliminates the need to whitelist IPs, open any ports on your firewall or go through any changes in your tests.
> Sauce Connect is not only for accessing private servers. If you want all of your test traffic to be secure and reliable, even the Selenium instructions you send to Sauce, you can use Sauce Connect for that too!

Get Started:
---------------------

1.先下载[Sauce Connect](https://saucelabs.com/downloads/Sauce-Connect-latest.zip)

2.解压然后运行`java -jar Sauce-Connect.jar lihuazhang ba444e83-206c-4a9c-b008-4da920d7f852`

3. 确定tunnel start以后，接下去你就可以运行你的测试用例了。

我们再来看下刚刚那个测试用例，让程序navigate.to到本地。

```ruby
require 'rubygems'
require "test/unit"
require 'selenium-webdriver'

class ExampleTest < Test::Unit::TestCase
    def setup
        caps = Selenium::WebDriver::Remote::Capabilities.firefox
        caps.version = "5"
        caps.platform = :XP
        caps[:name] = "Testing Selenium 2 with Ruby on Sauce"

        @driver = Selenium::WebDriver.for(
          :remote,
          :url => "http://lihuazhang:ba444e83-206c-4a9c-b008-4da920d7f852@ondemand.saucelabs.com:80/wd/hub",
          :desired_capabilities => caps)
    end

    def test_sauce
        @driver.navigate.to "http://localhost:3000" # Set a local env.
    end

    def teardown
        @driver.quit
    end
end
```
再运行一遍，必然成功啊~

我们看下Sauce-Connect.jar的日志，可以看到这个tunnel起到了一个中转的作用。

```
2012-04-25 00:04:01,184 - connection to Sauce Connect server closed
2012-04-25 00:04:01,927 - old keepalive timer shutting down
2012-04-25 00:04:03,923 - Successful handshake with Sauce Connect server
2012-04-25 00:04:03,927 - resending 2 packets
2012-04-25 00:04:04,078 - Tunnel host version: 0.1.0, remote endpoint ID: 73b1e202231b41a3802aab8785caf15f
2012-04-25 00:05:19,002 - relative URL, constructing absolute from Host header
2012-04-25 00:05:19,003 - using constructed absolute URL: http://localhost:3000/
2012-04-25 00:05:19,007 - Could not proxy /, exception: java.net.ConnectException: Connection refused
2012-04-25 00:05:22,145 - relative URL, constructing absolute from Host header
2012-04-25 00:05:22,147 - using constructed absolute URL: http://localhost:3000/favicon.ico
2012-04-25 00:05:22,148 - Could not proxy /favicon.ico, exception: java.net.ConnectException: Connection refused
2012-04-25 00:05:22,589 - relative URL, constructing absolute from Host header
2012-04-25 00:05:22,592 - using constructed absolute URL: http://localhost:3000/favicon.ico
2012-04-25 00:05:22,594 - Could not proxy /favicon.ico, exception: java.net.ConnectException: Connection refused
2012-04-25 00:05:23,391 - GET http://feeds.bbci.co.uk/news/rss.xml?edition=int -> 200 (2847ms)
2012-04-25 00:05:53,084 - disconnected from Sauce Connect server
2012-04-25 00:05:53,085 - connection to Sauce Connect server closed
```

同样的，我们可以在Account里面看到这个测试用例的运行细节，如图：

![detail](/photos/sauce_detail1.png)

![detail](/photos/sauce_detail2.png)

不错吧，有兴趣，自己动手试试。
