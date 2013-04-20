---
layout: post
title: "使用 capybara 登陆百度"
date: 2013-02-12 17:14
comments: true
categories: Capybara
---

ShiLongLu 问你有什么办法测试百度的登录窗口呢？这是差不多2个月前的问题了，我一直忙于 Dota 圣剑传说。恰巧春节有时间看了下。

百度登陆的时候是弹出一个对话框，如图：

![](/photos/baidulogin.png)

但是这个对话框是用 iframe 实现的。

```html
<iframe height="100%" frameborder="0" width="100%" scrolling="no" src="http://www.baidu.com/cache/user/html/login-1.2.html" marginheight="0" marginwidth="0" id="login_iframe">
...
...
...
</iframe>
```


所以在填写用户名和密码的时候，需要先 switch 到这个 frame 上去， 

>When working inside a frame, you should use Capybara's within_frame method.


>- (Object) within_frame(frame_id)
>
>Execute the given block within the given iframe given the id of that iframe. Only works on some drivers (e.g. Selenium)
>
>Parameters:
>frame_id (String) — Id of the frame
>

所以我们的实现基本如下：

baidu.feature:

```ruby
Given I am on baidu.com
When I  click "登录"
And I switch to login form
And I enter "lihuazhang@hotmail.com" for "帐号"
And I enter "204646" for "密码"
And I click "登录" button
Then I should see "zhangzuichi"
```

step_definition.rb:

```ruby
When /^I enter "(.*?)" for "(.*?)"$/ do |value, key|
  if key == "帐号"
    within_frame 'login_iframe' do
      fill_in 'pass_login_username_0', :with => value
    end
  else
     within_frame 'login_iframe' do
      fill_in 'pass_login_password_0', :with => value
    end
  end
end

When /^I click "(.*?)" button$/ do |arg1|
  within_frame 'login_iframe' do
    click_button 'pass_login_input_submit_0'
  end
end
```

大致如此，在 firefox 下测试通过。

