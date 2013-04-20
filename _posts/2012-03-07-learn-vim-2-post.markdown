---
layout: post
title: vim学习 shiftwidth, tabstop, softtabstop和expandtab
category: Vim
comments: true
---
<p>
<h6>1. shiftwidth</h6>
<p>
这个是用于程序中自动缩进所使用的空白长度指示的。一般来说为了保持程序的美观，和下面的参数最好一致。同时它也是符号移位(>>)长度的制定者。
</p>
</p>

<p>
<h6>2. tabstop
</h6>
<p>
定义tab所等同的空格长度，一般来说最好设置成8，因为如果是其它值的话，可能引起文件在打印之类的场合中看起来很别扭。
除非你设置了 expandtab模式，也就是把tabs转换成空格，这样的话就不会一起混淆，不过毕竟制表符为8是最常用最普遍的设置，所以一般还是不要改。
(lz总是把它设置为4个空格)
</p>
</p>
<p>
<h6>3. softtabstop</h6>
<p>
如果我们希望改变程序中的缩进怎么办？shiftwidth和tabstop不一样的话，你会发现程序比较难看的。
这时候，softtabstop就起作用了。可以从vim的说明中看到，一旦设置了softtabstop的值时，你按下tab键，
插入的是空格和tab制表符的混合，具体如何混合取决于你设定的softtabstop，举个例子，如果设定softtabstop=8,
那么按下tab键，插入的就是正常的一个制表符;如果设定 softtabstop=16,那么插入的就是两个制表符；如果softtabstop=12,
那么插入的就是一个制表符加上4个空格；如果 softtabstop=4呢？那么一开始，插入的就是4个空格，此时一旦你再按下一次tab，
这次的四个空格就会和上次的四个空格组合起来变成一个制表符。
换句话说，softtabstop是“逢8空格进1制表符”,前提是你tabstop=8。
</p>
</p>
<p>
<h6>4. expandtab</h6>
<p>
因为tab的大小不一样，在多人一起开发项目时，为了使代码风格尽量保持一致，
一般不允许在代码使用TAB符，而以4个空格代之。
</p>
</p>

<p>一般的shiftwidth, tabstop, softtabstop和expandtab的配置</p>
<pre class="prettyprint">
set shiftwidth=4                " use indents of 4 spaces
set tabstop=4                   " an indentation every four columns
set softtabstop=4               " let backspace delete indent
set expandtab                   " tabs are spaces, not tabs
</pre>
