---
layout: post
title: "surround.vim"
date: 2012-08-05 09:37
comments: true
categories: Vim
---

一般写东西的时候，总会遇到比如写了一长串文字后，突然想给这串文字头尾加个括号，
或者引号。其实很多编辑器都提供这种功能，选中，然后输入括号。但是一直用 Vim，也没发现
有这种功能， 也只能怪自己没有研究。 后来网上找了一圈， 发现了原来有这个插件： [surround.vim](https://github.com/tpope/vim-surround/)。

>Surround.vim is all about "surroundings": parentheses, brackets, quotes, XML tags, and more. The plugin provides mappings to easily delete, change and add such surroundings in pairs.

先看下全部的快捷键：

![](/photos/allkey.png)

可以看到基本有三种操作：

+ 删除

ds 加包在两边的引号，括号或者标签（标签全部用t代替）。

e.g:

"hello, world!" => hello, world! 只要输入 `ds"`

\<strong\>hello, world!\</strong\> => hello, world! 只要输入 `dst`

+ 添加

![](/photos/addkey.png)

note: s 代表sentence iw/w 代表 word。

+ 修改

![](/photos/changekey.png)

截图来自[你应该知道的vim插件之surround.vim](http://ihacklog.com/post/vim-plugins-you-should-know-about-surround.html)

到此， surround vim 你就基本掌握了。 如果你对这些快捷键能倒敲入流，那么你一定能得到许多的便捷。 XD

扩展阅读： Text Object
<!-- more -->

<h2>Vim 的 text object</h2>

除了和单个字符打交道外， 我们还处理单词， 句子还有段落， 这些在 Vim 中就是<strong>文本对象</strong>。
Vim 中纯文本和常见编程语言都有文本对象。也可以利用 Vim 脚本自定义新的文本对象。

Vim 中，编辑命令有如下结构：

```vim
<number><command><text object or motion>
```

<strong>number</strong> 用于将命令使用于多个文本对象或移动，如，向后移动三个单词、向前移动两个段落。 number 是可选项，且可以出现在命令的前面或后面。

<strong>command</strong> 是一个操作，如，修改、删除(剪切)，或复制。 command 也是可选的，但如果没有，就只是移动命令，而不是编辑命令。

<strong>text object</strong> 或 motion 可以是一个文本结构，如单词、句子、段落或移动， 如，向前移行，向后一页，行尾。

<strong>editing command</strong> 即一个命令加上一个文本对象或移动，如，删除一个单词，修改下一个句子， 复制这个段落。

<h3>普通文本对象</h3>
普通文本中， Vim 提供三种行为模块： words, sentences and paragraphs.

<strong>Words</strong>

+    aw – 环绕单词对象 (包括单词外面的空格字符)
+    iw – 在单词对象内部 (不包括单词外面的空格字符)

`hello world vim!`

在world单词中间敲打 `daw`

`hello_vim!` 注意： 一个空格,下划线代替


`hello world vim!`

在world单词中间敲打 `diw`

`hello__vim!` 注意： 两个空格，下划线代替

<strong>Sentences</strong>

+ as - 环绕句子对象
+ is - 在句子对象内部

和 Words 同意的道理。 a 表示around， i 表示inner。

<strong>Paragraphs</strong>

+ ap – 环绕段落对象
+ ip – 在段落对象内部

和 Words 同意的道理。 a 表示around， i 表示inner。

<h3>编语言文本对象</h3>

Vim 基于常见变成语言结构提供了一些文本对象。

<strong>字符串</strong>

+ a" - 双引号括起来的字符串
+ i' - 双引号括起来的字符串内
+ a" - 单引号括起来的字符串
+ i' - 单引号括起来的字符串内
+ a` - 反向引号括起来的字符串
+ a` - 反向引号括起来的字符串内

光标不需要在引号内，命令默认会寻找当前行的第一个引号。

<strong>圆括弧</strong>

+ a) - 一个括弧块
+ i) - 括弧块内
+ % 移动是另一种匹配括弧块的方法。 c% 与 ca( 相同。不同点也在于对光标位置的要求。

<strong>方括弧</strong>

+ a] - 一个方括弧块
+ i] - 方括弧块内

<strong>花括弧</strong>

+ a} - 一个花括弧块
+ i} - 花括弧内

<strong>标记语言标签</strong>

+ at - 一个标签块
+ it - 标签块内
+ a&gt; - 单个标签
+ i&gt; - 单个标签内

其实可以参见:

+ [ Vim Text Objects: The Definitive Guide ](http://blog.carbonfive.com/2011/10/17/vim-text-objects-the-definitive-guide/)
+ [(译) Vim Text Objects - The Definitive Guide](http://zwb.me/blog/learn-to-speak-vim/)
