---
layout: post
title: "Capybara+Cucumber+WebDriver自动化测试框架"
date: 2012-04-19 21:13
comments: true
categories: Capybara
---


我面试的时候，BOSS对我说，我们是要求自动化的。我想这我会啊。BOSS又说，我们也有一套自动化的框架。我想那更好。然后等我入职之后，发现毛框架也没有，只有四个完全不会代码的测试攻城狮。

老板说要自动化，我说自动化啊？我花了几天的时间温习了我熟悉的`JAVA+Junit+SeleniumRC`，否定了这个框架，一来语言太重，二来都不会coding。我很苦恼地向BOSS抱怨了，他说用脚本语言吧，于是我就想要不就用`python+pyunit+webdriver`，在google打工的时候，接触的就是这个框架。BOSS想了想，说用ruby吧。原因在他是ruby的超级粉丝。

于是我找到了`cucumber+capybara+webdriver`，这也是我第一次接触ruby，第一次接触BDD。而我选用这个框架的原因如下：
    1. cucumber有一套自己的DSL，任何会写测试用例的人都会写。
    2. 对于这套框架要学的ruby知识，学起来相对容易。
    3. 公司文化。

前面都是废话，下面开始进入正题。

##功于善其事，必先利其器

* 首先搭建环境，确定有ruby的环境。安装必要的gem
```bash
gem install cucumber 
gem install capybara
gem ins tall selenium-webdriver
```

*[Cucumber](http://cukes.info/) [Capybara](https://github.com/jnicklas/capybara) [WebDriver](http://code.google.com/p/selenium/)*

* 建立个空项目，将gem加入gemfile

```ruby
source "http://ruby.taobao.org/"
gem 'cucumber', '>= 1.1.9'
gem "capybara", "~> 1.1.2"
gem 'selenium-webdriver', '>= 2.20.0'
```

* 先简单介绍一下cucumber

我们在这个空项目下运行`cucumber`

```bash
zhangmatoMacBook-Pro:test lihuazhang$ cucumber
You don't have a 'features' directory.  Please create one to get started.
See http://cukes.info/ for more information.
```

可以看出cucumber会去寻找`features`目录，先创建一个features目录。

```bash
zhangmatoMacBook-Pro:test lihuazhang$ mkdir features
zhangmatoMacBook-Pro:test lihuazhang$ ls
features
zhangmatoMacBook-Pro:test lihuazhang$ cucumber
0 scenarios
0 steps
0m0.000s
```

因为在features下面什么都没有，所以运行结果都是零。

那我们创建一个feature，cucumber把feature都命名为xxx.feature, 而每个feature有多个scenario。

```ruby
Feature: Adding

    Scenario: Add two numbers
        Given the input "2+2"
        When the calculator is run
        Then the output should be "4"
    Scenario: Minus two numbers
        Given the input "2-1"
        When the calculator is run
        Then the output should be "0"
```

cucumber使用`Given/When/Then`这种叫做[gherkin](https://github.com/cucumber/cucumber/wiki/Gherkin)的可描述性语言(DSL)。

feature文件中的每个Given/When/Then步骤在Step文件中都有对应的Ruby执行代码，两类文件通过正则表达式相关联。后面马上讲到。

让我们再来运行一遍

```ruby

zhangmatoMacBook-Pro:test lihuazhang$ cucumber 
Feature: Adding

  Scenario: Add two numbers       # features/test.feature:3
    Given the input "2+2"         # features/test.feature:4
    When the calculator is run    # features/test.feature:5
    Then the output should be "4" # features/test.feature:6

  Scenario: Minus two numbers     # features/test.feature:7
    Given the input "2-1"         # features/test.feature:8
    When the calculator is run    # features/test.feature:9
    Then the output should be "0" # features/test.feature:10

2 scenarios (2 undefined)
6 steps (6 undefined)
0m0.005s

You can implement step definitions for undefined steps with these snippets:

Given /^the input "([^"]*)"$/ do |arg1|
  pending # express the regexp above with the code you wish you had
end

When /^the calculator is run$/ do
  pending # express the regexp above with the code you wish you had
end

Then /^the output should be "([^"]*)"$/ do |arg1|
  pending # express the regexp above with the code you wish you had
end

If you want snippets in a different programming language,
just make sure a file with the appropriate file extension
exists where cucumber looks for step definitions.

```
可以看到我们运行了2个scenarios,6个steps，都没有定义，于是cucumber仁慈的告诉了我们如何实现。于是我们就要开始写step了。

```ruby
#小小作弊下，就不写的漂亮了。
Given /^the input "([^"]*)"$/ do |statement|
    @answer = eval statement
end

When /^the calculator is run$/ do
    puts "Run~"
end

Then /^the output should be "([^"]*)"$/ do |answer|
    @answer == answer
end

```

我们再来运行一遍

```ruby
zhangmatoMacBook-Pro:test lihuazhang$ cucumber 
Feature: Adding

  Scenario: Add two numbers       # features/test.feature:3
    Given the input "2+2"         # features/step_definitions/test_step.rb:1
    When the calculator is run    # features/step_definitions/test_step.rb:5
      Run~
    Then the output should be "4" # features/step_definitions/test_step.rb:9

  Scenario: Minus two numbers     # features/test.feature:7
    Given the input "2-1"         # features/step_definitions/test_step.rb:1
    When the calculator is run    # features/step_definitions/test_step.rb:5
      Run~
    Then the output should be "0" # features/step_definitions/test_step.rb:9

2 scenarios (2 passed)
6 steps (6 passed)
0m0.005s

```

哇，恭喜都通过了。

好，关于cucumber的介绍就到这里，具体可以去看看[Cucumber的目录结构和执行过程](http://www.cnblogs.com/CloudTeng/articles/2214293.html)

* 在简单介绍下Capybara

Capybara是水豚的意思，在水豚之前有一个老鼠，叫做webrat，你如果在google上搜索`capybara webrat`的话，会发现一大推类似
<blockquote>
Cucumber: Switching from Webrat to Capybara - cakebaker
cakebaker.42dh.com/.../cucumber-switching-from... - 网页快照 - 翻译此页
19 Sep 2010 – Fortunately, there is an alternative working with both Rails versions: Capybara. And so I decided to make the switch from Webrat to Capybara.
</blockquote>

Webrat逐渐被Capybara取代，就像selenium逐渐被webdriver取代。

Capybara主要是用来测试rails和Rack应用的。官网上说

<blockquote>
Capybara helps you test Rails and Rack applications by simulating how a real user would interact with your app. It is agnostic about the driver running your tests and comes with Rack::Test and Selenium support built in. WebKit is supported through an external gem.
</blockquote>

我们主要用Capybara和Webdriver结合起来进行网页自动化测试。（注意：Capybara不支持selenium rc）

详细请见[Capybara](https://github.com/jnicklas/capybara),里面讲的非常详细了。


##小试牛刀
在了解了cucumber和capybara之后，我们可以正式开始写点东西。 在空项目底下，建立类似的目录结构

```bash

├── features
    ├── example
       ├── example.feature
       ├── step_definitions
       │   └── example_steps.rb
       └── support
           └── env.rb
```

我们这个例子是访问百度

```ruby

Feature: Using baidu

Scenario: Searching for a term

Given I am on baidu.com
When I enter "pizza"
And I press "百度一下"
Then I should see "pizza_百度百科"
```

Given/When/And/Then很明了的告诉我们，打开百度，输入pizza，然后点击百度一下，然后就能看到pizza_百度百科。

```ruby
Given /^I am on baidu\.com$/ do
  Capybara.app_host = "http://www.baidu.com"
  visit('/')
end

When /^I enter "([^"]*)"$/ do |term|
  fill_in('wd',:with => term)
end

When /^(?:|I )press "([^"]*)"$/ do |button|
  puts button
  click_button(button)
end

Then /^(?:|I )should see "([^"]*)"$/ do |text|
  if page.respond_to? :should
    page.should have_content(text)
  else
    assert page.has_content?(text)
  end
end
```

接着就是值得一提的env.rb，这是cucumber的环境配置文件。
<blockquote>
The file features/support/env.rb is always the very first file to be loaded when Cucumber starts a test run. You use it to prepare the environment for the rest of your support and step definition code to operate. 
</blockquote>

```ruby
require 'capybara'
require 'capybara/cucumber'
require 'capybara/dsl'

Capybara.app_host = "http://www.baidu.com"
Capybara.default_driver = :selenium
```

让我们来运行一下


```ruby
zhangmatoMacBook-Pro:features lihuazhang$ cucumber example/example.feature 
Feature: Using baidu

  Scenario: Searching for a term   # example/example.feature:4
    Given I am on baidu.com        # example/step_definitions/example_steps.rb:1
    When I enter "pizza"           # example/step_definitions/example_steps.rb:6
    And I press "百度一下"             # example/step_definitions/example_steps.rb:10
      百度一下
    Then I should see "pizza_百度百科" # example/step_definitions/example_steps.rb:15

1 scenario (1 passed)
4 steps (4 passed)
0m9.184s
```

Nice!

至此，基本的框架搭好了。至于如何配置不同的浏览器，以及如何和page object配合，我们以后再讲。
