---
layout: post
title: "ruby 的 Heredocs"
description: ""
categories: ruby
tags: []
---

看 [Capistrano](https://github.com/capistrano) 源码， 在 `capistrano / bin / capify` 下，发现
`here doc` 的写法，

```ruby
...
...
def unindent(string)
  indentation = string[/\A\s*/]
  string.strip.gsub(/^#{indentation}/, "")
end
...
...

 "Capfile" => unindent(<<-FILE),

    load 'deploy'

    # Uncomment if you are using Rails' asset pipeline
    # load 'deploy/assets'

    load 'config/deploy' # remove this line to skip loading any of the default tasks
  FILE

```


孤陋寡闻的我立马google 了一把找到篇好文，抄下来分享：

Heredocs come in handy when you have to deal with
larger multi-line strings in the source code itself. 
However, it usually breaks the indents:

```ruby
class Poem
  def initialize
    @text = <<END
"Faith" is a fine invention
When Gentlemen can see
But Microscopes are prudent
           In an Emergency.
(Emily Dickinson 1830-1886)
END
  end
  def recite
    puts @text
  end
end
```

But it wouldn’t be Ruby if there were no way to make this pretty. 
The minus in -END makes sure any whitespace before the end marker is ignored
and the first six spaces of every line are cut when the string collected
by heredoc is post processed with gsub:

```ruby

class Poem
  def initialize
    @text = <<-END.gsub(/^ {6}/, '')
      "Faith" is a fine invention
      When Gentlemen can see
      But Microscopes are prudent
                 In an Emergency.
      (Emily Dickinson 1830-1886)
    END
  end
  def recite
    puts @text
  end
end

```

The result for both snippets is exactly 
the same – provided you stick to the recommended 2 spaces indent for Ruby source code:

```ruby

>> Poem.new.recite
"Faith" is a fine invention
When Gentlemen can see
But Microscopes are prudent
           In an Emergency.
(Emily Dickinson 1830-1886)

```


