---
layout: post
title: vim学习 listchars
category: Vim
comments: true
---
<p>
在ruby-china.org混，看到很多vimer，于是就请教了如何学习vim，@jinleileiking同学就给了建议，非常中用，他说：
<div class="comment">
<ul>
<li>vimcasts.org 类似railscasts</li>
<li>[学习vi和Vim编辑器(第7版)].(Learning.the.vi.and.Vim.Editors.7th.Edition).A.Robbins&E.Hannah&L.Lamb.文字版</li>
<li>[Hacking.Vim].[Hacking.Vim].[Hacking.Vim].Packt.Publishing.Hacking.Vim.May.2007</li>
<li>:help vim内置帮助</li>
</div>
</ul>
</p>

<p>
    先看vimcasts.org, 第一章：[显示不可视的字符] (http://vimcasts.org/episodes/show-invisibles/)
<pre class="prettyprint">
" 设置显示空格，tab的切换键
nmap &lt;leader&gt;l :set list!<CR>

" 设置tab用小三角加重复的空格显示，换行用横折符号显示。可以:help listchars
set listchars=tab:▸\ ,eol:¬
</pre>
</p>
