---
layout: post
title: "Word Wrap Kata Refactoring"
date: 2015-02-16
comments: true
categories:
---

In my last [post](http://www.lisahamm.com/blog/2015/02/12/word-wrap-kata-in-ruby/), I walked through the steps involved in Uncle Bob's [Word Wrap Kata](http://thecleancoder.blogspot.com/2010/10/craftsman-62-dark-path.html). In this post, I will continue with some refactoring steps specific to a solution written in Ruby.

### The Starting Point

Completing the initial steps of the kata in Ruby resulted in the following code:

<!--more-->

```ruby
def self.wrap(string, line_width)
  return string if string.length <= line_width
  if string[0...line_width].index(" ") != nil
    space_index = (line_width-1) - string[0...line_width].reverse.index(" ")
    string[0...space_index] + "\n" + wrap(string[space_index+1..-1], line_width)
  elsif string[line_width] == " "
    string[0...line_width] + "\n" + wrap(string[line_width+1..-1], line_width)
  else
    string[0...line_width] + "\n" + wrap(string[line_width..-1], line_width)
  end
end
```

### Practice With Monkey Patching

For learning purposes, my mentor suggested I add my own methods to Ruby's String class (a process often referred to as monkey patching), so I could see how the process works (but note that in practice this is usually not a good idea). To start, I added two methods to Ruby's string class: a method to return the last index of a given character in a string (similar to Java's lastIndexOf() method) and a method to return the rest of a string from a given index:

```
class String
  def last_index_of(character)
    self.length-1 - self.reverse.index(character)
  end

  def rest_from(index)
    self[index..-1]
  end
end
```

### Optimizing the last_index_of(character) Method

Further refactoring can be done to the last_index_of(character) method. I removed the 'self' reference since Ruby will call methods without explicit receivers on whatever object is currently assigned to 'self,' which in this case is an instance of the string class, making it unnecessary to reference 'self' in this method. Next, instead of using Ruby's reverse method, we can use the downto method. This is a less expensive operation since we can bypass the reversal process and simply tell ruby where to begin iterating in the reverse direction.

```
def last_index_of(character)
  (length - 1).downto(0) {|i| return i if self[i] == character}
  nil
end
```
Next, I incorporated these additional methods into my wrap function:

```
def self.wrap(string, line_width)
  return string if string.length <= line_width
  if string[0...line_width].index(" ") != nil
    space_index = string[0...line_width].last_index_of(" ")
    string[0...space_index] + "\n" + wrap(string.remainder_from(space_index+1), line_width)
  elsif string[line_width] == " "
    string[0...line_width] + "\n" + wrap(string.remainder_from(line_width+1), line_width)
  else
    string[0...line_width] + "\n" + wrap(string.remainder_from(line_width), line_width)
  end
end
```
I also added a final method to the String class to return a substring:

```
def substring(start_index=0, end_index)
  self[start_index..end_index]
end
```

### Ruby's strip method

Ruby has a handy method, 'strip,' in the String class that will eliminate whitespace at the start and end of a string. I decided to take advantage of this and was able to eliminate the previous steps in the method that served this purpose.

```
def self.wrap(string, line_width)
  return string if string.length <= line_width
  substring = string.substring(0, line_width-1)
  end_index = substring.index(" ") != nil ? substring.last_index_of(" ") : line_width
  string.substring(end_index-1) + "\n" + wrap(string.rest_from(end_index).strip, line_width)
end
```

### Readability

Another important concept to consider when refactoring is the code readability. Is the code readable? Will the code be easy to read if you look back at it in months from now? Someone who does not know how to program should be able to read your code and know what it is accomplishing. In this particular example, one way to make the code more readable is to add an additional method to the word wrap module that checks if a string contains a space character. The method should return a boolean value:

```
def self.has_a_space?(string)
  string.index(" ") != nil
end
```

### Putting it all together

Putting all of this together results in the following code:

```
class String
  def last_index_of(character)
    (length - 1).downto(0) {|i| return i if self[i] == character}
    nil
  end
  def rest_from(index)
    self[index..-1]
  end

  def substring(start_index=0, end_index)
    self[start_index..end_index]
  end
end

module WordWrap
  def self.wrap(string, line_width)
    return string if string.length <= line_width
    substring = string.substring(line_width-1)
    end_index = has_a_space?(substring) ? substring.last_index_of(" ") : line_width
    string.substring(end_index-1) + "\n" + wrap(string.rest_from(end_index).strip, line_width)
  end

  def self.has_a_space?(string)
    string.index(" ") != nil
  end
end
```

My solution to this kata, including the tests, is available on my [github](https://github.com/lisahamm/word_wrap_kata).
