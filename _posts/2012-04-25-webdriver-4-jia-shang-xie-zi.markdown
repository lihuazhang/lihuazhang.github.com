---
layout: post
title: "WebDriver 4: 加上叶子"
date: 2012-04-25 20:52
comments: true
categories: WebDriver 
---
在前三篇WebDriver文章里，我们基本上有了一个Maven WebDriver Junit4的框架主干，这些还远远不够。
所以我们要添些叶子。

+ 要有资源配置文件
+ 要有WebDriver的封装
+ 要有业务对象的抽象，比如用户
+ 要有自定义的异常

我们大概期望有这样的文件架构：

```
.
├── main
│   ├── java
│   │   └── com.ahchoo.automation.exception
│   │   └── com.ahchoo.automation.model
│   │   └── com.ahchoo.automation.page
│   │   └── com.ahchoo.automation.utils
│   └── resources
└── test
    ├── java
    │   └── com.ahchoo.automation.testcase
    └── resources
```

-------


###配置文件

Maven 有自己的资源文件管理策略，我们只需要修改之前的pom.xml

```xml

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    ...
    <build>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
            </resource>
        </resources>
        <testResources>
            <testResource>
                <directory>src/test/resources</directory>
            </testResource>
        </testResources>
    </build> 
    ...
</project>

```

我们指定了两个资源文件目录，然后将config.xml放在这些目录之下。
*注意，src/main/resouces下的文件只能在运行main里面的代码的时候才会用到，同样的src/test/resources下的配置文件只有在运行测试的时候才会加载。*

可以将**浏览器类型, 远程服务器地址, 应用URL之类**放到config.xml中去。

```xml

<config id="config">
    <BaseUrl>http://www.baidu.com</BaseUrl>
    <Browser>
        <type>Firefox10</type>
        <location>pathToBrowser</location>
        <remote>192.168.1.248</remote>
    </Browser>
    <user name="xxx" password="xxx" />
</config>

```

