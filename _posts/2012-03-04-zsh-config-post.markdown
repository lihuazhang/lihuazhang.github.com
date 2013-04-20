---
layout: post
title: oh my zsh
category: zsh
comments: true
---

<p>Mac自带zsh， 先设置你的默认shell为zsh</p>
<pre class="prettyprint">
chsh -s /bin/zsh
</pre>
<p>
然后使用oh-my-zsh的配置。参见<a href="https://github.com/robbyrussell/oh-my-zsh/">oh my zsh</a>
<pre class="prettyprint">
cd ~
curl -L https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh | sh</pre>
</p>
<p>修改.zshrc,把你需要的环境变量配置全部加到.zshrc中去。定义自己的主题。可以在.oh-my-zsh/themes下面找到所有的主题
同时你也可以去<a href="https://github.com/robbyrussell/oh-my-zsh/wiki/themes">themes</a>里去找你喜欢的主题。个人比较喜欢gnzh.
<pre class="prettyprint">
zhangmatoMacBook-Pro.local ~/.oh-my-zsh/themes  ‹master› 
Soliah.zsh-theme              daveverwer.zsh-theme          frisk.zsh-theme               josh.zsh-theme                mikeh.zsh-theme               rgm.zsh-theme                 suvash.zsh-theme
afowler.zsh-theme             dieter.zsh-theme              funky.zsh-theme               jreese.zsh-theme              miloshadzic.zsh-theme         risto.zsh-theme               takashiyoshida.zsh-theme
...
</pre>
</p>
<p>安装插件，在.zshrc文件里找到"plugins=",然后在里面添加你所需要的插件，你安装的插件可以在.oh-my-zsh/plugins下面找到。
<pre class="prettyprint">
plugins=(rails3 rails git textmate ruby rvm gem git github brew bundler textmate pow)
</pre>
</p>

<p>
重启下终端，然后就可以使用订制好的zsh了。
</p>
<p>
<img src="/photos/zsh.png"></img>
</p>
