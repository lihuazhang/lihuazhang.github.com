---
layout: post
title: "Jmeter 兵器谱之 Regular Expression Extractor"
category: Jmeter
tags: [test-china.org]
---

接着我之前的血案，无意间学会了如何使用Jmeter里面的Regular Expression Extractor。
搞定了Jmeter脚本的一些基本配置，然后我开始跑脚本。非常顺利，但是问题也出现了，
有个统计多少用户登陆的功能始终显示用户数量稳定在一个数字，没有增加。
仔细观察了result tree里面的结果（个人十分喜欢result tree，因为可把request，response看个通透。），
发现，由于脚本是录制下来的，所以每次发送的token都是同一个，所以系统自然认为是同一个用户不断的在登陆。

需求出来了，我需要从某个httprequest的response中提取部分参数，作为下一个request的部分参数。

![alt text][1]

如图，第二个str后面的字串就是用户的token，我需要把它传入下一个request的参数中去。

![alt text][2]


在需要获取response参数的那个request下面挂上一个Regular Expression Extractor.

![alt text][3]

对Regular Expression Extractor界面的一些地方做下说明：

![alt text][4]

`response field to check`: 需要你自己选定是对response的内容还是头，还是url进行抓取。

`reference name`： 抓取出来的变量名称，自己取名，因为之后你需要用到。

`regular expression`： 这个是最重要的，正则表达式就写在这里。 正则表达式的正确与否，是你抓取的关键。

`template`： 提取正则表达式里面的内容，通常我们只提取一个字串，所以通常都是$1$。

`match number`： 匹配数字， 通常我们只提取一个字串，所以写1无妨。

`Default Value`: 缺省值，用于没有抓取到值时的处理。

这样，我们通过`{var}`的方式，把变量写入request里面。

![alt text][5]

然后，跑了一下整个脚本，发现一切都正常了，这场“血案”暂告一个段落。

  [1]: https://reeg0a.bay.livefilestore.com/y1pp8EmQ60SdKGa1IAg4u-62jYlLwlwf_j9CIojNOdilYMYzzEHKVzs6SpQv70pNxf4HwN-KfAhPPzSrkrv-_XP-eHRyyvOHJmO/response.JPG?psid=1
  [2]: https://recfxa.bay.livefilestore.com/y1pMynMzxahrPPlvf4yzXo7Wp6ehUhL5E8Y8bFtYtuAmay5r6xKWE90E95V2bGFf8MbdYs1pkWuxGCPDVr4ohwdRwg1rKuBRc01/request.JPG?psid=1
  [3]: https://reenfw.bay.livefilestore.com/y1p0k0lbPYe3AVDtDOl9t_Yp_ZDGcKKNjpAO-KsipikIOC9XCtmxOWIL6Oscwnk67ShHQjKsg6R7-lX9VuvgPaInhc6EfiFg4oc/addComponent.jpg?psid=1
  [4]: https://redcja.bay.livefilestore.com/y1pbIsumJXdcJ3U79vtaIyLRUl3cxh-CR20K5sIssdtZFkS0mz-664UjZQ9PRGLJvkepu8RNeQJMfCMpDKGNhfH_2H6SBXDQX1e/REextractor.JPG?psid=1
  [5]: https://refwuq.bay.livefilestore.com/y1pORO3eVDUFU0HTGOOAoofQ_ko1kO9b5yE1wQMZAuLm5aDbWhLJFgps8_ad4sy2YzYMoDz9yUKT7Rn0reT37sA9XKwI-PLST79/request2.JPG?psid=1
