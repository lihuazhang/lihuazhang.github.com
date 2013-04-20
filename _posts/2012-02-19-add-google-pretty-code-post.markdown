---
layout: post
title: "Add google pretty code support"
date: 2012-04-19 18:18
comments: true
categories: Jekyll
---

*Jekyll* 本身是不支持语法高亮的。而用 *Pygments* 又要装插件，看上去也挺复杂的。于是去网上搜索“博客语法高亮”，
然后发现了 `google code prettify`，心想 google 出品，质量必定可靠.具体请看下<a href="http://code.google.com/p/google-code-prettify/"><strong>google code prettify</strong></a>。


这里就介绍一下，如何使用的。
<ul>
<li>从http://code.google.com/p/google-code-prettify/downloads/list下载最新的代码。</li>
<li>解压出来，得到
<pre>
lang-apollo.js
lang-clj.js
lang-css.js
lang-go.js
lang-hs.js
lang-lisp.js
lang-lua.js
lang-ml.js
lang-n.js
lang-proto.js
lang-scala.js
lang-sql.js
lang-tex.js
lang-vb.js
lang-vhdl.js
lang-wiki.js
lang-xq.js
lang-yaml.js
prettify.css
prettify.js
sunburst.css
</pre>
</li>
<li>把这些文件放到你的项目的目录下去。 </li>
<li>把css文件和js文件应用到页面上去。
<pre class="prettyprint">
&lt;link href="/js/google-code-prettify/sunburst.css" type="text/css" rel="stylesheet"/&gt;
&lt;script type="text/javascript" src="/js/google-code-prettify/prettify.js"&gt;&lt;/script&gt;
</pre>
在body上加一个onload函数
<pre class="prettyprint">
&lt;body onload="prettyPrint()" &gt;
</pre>
</li>
<li>然后在要高亮的代码块使用提供的tag，比如：

```html
<pre class="prettyprint">
#include <stdio.h>

/* the n-th fibonacci number.
 */
unsigned int fib(unsigned int n) {
    unsigned int a = 1, b = 1;
    unsigned int tmp;
    while (--n >= 0) {
        tmp = a;
        a += b;
        b = tmp;
    }
    return a;
}

main() {
    printf("%u", fib(10));
}

</pre>
```

你不需要指定语言环境,因为 prettyprint() 会对此进行猜测. 你也可以使用 prettyprint 这个类通过指定语言的拓展名来指定语言。
</li>
<li>
你看到的结果就是：

```c
#include <stdio.h>

/* the n-th fibonacci number.
 */
unsigned int fib(unsigned int n) {
    unsigned int a = 1, b = 1;
    unsigned int tmp;
    while (--n >= 0) {
        tmp = a;
        a += b;
        b = tmp;
    }
    return a;
}

main() {
    printf("%u", fib(10));
}
```

</li>
</ul>
</p>
<p>
看上去不错吧，你也去试试吧！
</p>

</p>
</pre></li>
</ul>
</strong></p>
