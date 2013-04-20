---
layout: post
title: vim配置
category: Vim
comments: true
---

<p>一直用vi，也没仔细研究过，也不会配置，在网上发现<a href="http://spf13.com/project/spf13-vim"><strong> spf13-vim </strong></a>,安装配置十分方便，于是便想推荐大家使用。</p>
<p>只需在终端敲打
    <pre class="prettyprint">curl https://raw.github.com/spf13/spf13-vim/3.0/bootstrap.sh -L -o - | sh </pre>
    等待数分钟，安装全部完成。具体细节不介绍，spf13-vim的网站上有详细介绍。
</p>
<p>结束之后还需要.vimrc里加入
<pre class="prettyprint">
set t_Co=256
</pre>
</p>

<p>
使用spf13-vim需要vim支持python，mac下的vim本身是不支持python的，如何验证vim支持python。
<pre class="prettyprint">
:python print "Hello, world!"
</pre>
看到输出结果是Hello，world!就是支持python了。
如果不知道的话，那就要安装一个支持python的vim
<pre class="prettyprint">
sudo port install vim +python +ruby
</pre>
安装之后，使用vi，如果发现有libiconv的问题，把你env里面的DYLD_LIBRARY_PATH去掉。
</p>

<p>全部搞定，在用用你的vi看看，是不是很惊艳？</p>
<img src="/photos/vim.png"></img>
