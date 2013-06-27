Heredocs come in handy when you have to deal with larger multi-line strings in the source code itself. However, it usually breaks the indents:

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


