---
layout: post
title: vim学习 jsctags
category: Vim
comments: true
---

<p>
ctags不能非常胜任对现在的javascript代码的解析，尤其是遇到：
<pre class="prettyprint">
Test.object.do = function(obj) {// ...}
ajaxSetup: function( settings ) {//..}
</pre>
网上搜索了下，发现了doctorjs.git (以前叫jsctags), 号称被应用于Cloud9 IDE's Ace online editor.
<div class="comment">
<ul>
  <li>安装doctorjs需要nodejs环境，如何安装nodejs自己google去</li>
  <li>安装ctags，brew也好macports也好。</li>
  <li>git  clone https://github.com/mozilla/doctorjs.git --recursive</li>
  <li>make install</li>
  <li>Add export NODE_PATH=/usr/local/lib/jsctags/:$NODE_PATH to your .zshrc or .bashrc</li>
  <li>然后就可以使用了，直接jsctags your_js_dir</li>
  <li>配合vim的tagbar非常好用。</li>
</ul>
</div>

</p>
