---
layout: post
title: "Word Wrap Kata in Ruby"
date: 2015-02-12
comments: true
categories:
---

I've recently been working through the [Word Wrap Kata](http://thecleancoder.blogspot.com/2010/10/craftsman-62-dark-path.html), writing it in Ruby. Since [Uncle Bob's post](http://thecleancoder.blogspot.com/2010/10/craftsman-62-dark-path.html) walks through the kata in Java, I thought I'd write a post detailing an implementation in Ruby.
<!--more-->

### The General Concept

The idea behind this kata is to write a method that will wrap a string of text according to a specified line length/width. The wrap method will take a string and line length as parameters. It must return the string with appropriate line breaks, ensuring no line is longer than the specified line length. The concept is similar to that of a word processor (i.e. Microsoft Word). Uncle Bob uses TDD to demonstrate the benefit of taking small steps when writing code. If the step you are trying to take in your code seems more like a leap, there is likely a simpler step that could (and should) be taken.

### The First Two Tests

What is the simplest possible case the method must handle? An empty string and line length of zero. So our first test would be:

```ruby
it "does not wrap an empty string onto a newline" do
  expect(WordWrap.wrap("", 0)).to eq ""
end
```
In order to make this test pass, the wrap method must be created and just needs to return an empty string at this point:

```
def self.wrap(string, line_width)
  ""
end
```
Building upon this, the method should also return an unaltered string when given a string shorter than the desired line length. The second test documents this:

```
it "does not wrap a string shorter than the line width onto a newline" do
  expect(WordWrap.wrap("word", 6)).to eq "word"
end
```
Passing the second test is straightforward: the method should simply return the string specified in its parameters.

```
def self.wrap(string, line_width)
  string
end
```

### Going Down The Wrong Path

The third test is where this kata really starts to get interesting. When coming up with the next simplest test here, you may find yourself going down the wrong path. To some, the obvious next test is for wrapping a two word string after the space. This test would likely be followed by wrapping a three word string after the first space.

```
it "wraps a two word string after the space" do
  expect(WordWrap.wrap("word word", 6)).to eq "word\nword"
end

it "wraps a three word string after the first space" do
  expect(WordWrap.wrap("word word word", 6)).to eq "word\nword\nword"
end
```
Once these two tests are passing, we know we can wrap a string at the first space, so it's time to move on to wrapping at the second space:

```
it "wraps a three word string after the first space" do
  expect(WordWrap.wrap("word word word", 11)).to eq "word word\nword"
end
```
While it isn't very hard to pass the tests for wrapping a string at its first space, it's much more difficult to get the test for wrapping at the second space to pass. Even if you are confident you can figure out how to pass this last test, it's going to require you making a leap with your code. When you find yourself adding a lot of code at once, and perhaps, making guesses in your code, it is time to take a step back and look for something simpler to solve. As Uncle Bob quotes in his post, "When faced with a problem you do not understand, do any part of it you do understand, then look at it again."

### Taking A Step Back

Lets revisit the third test: wrapping a two word string after the space. Is there something else that could be tested at this point instead? Another case the method needs to handle is wrapping a one word string longer than the specified line length. Making this the third test case will end up leading us down a clearer path.

```
it "wraps a string longer than the line length" do
  expect(WordWrap.wrap("word", 2)).to eq "wo\nrd"
end
```
To pass this test, the method returns an unaltered string if its length is less than or equal to that of the specified line length, otherwise it will split the characters of the string with a newline at the line length boundary.

```
def self.wrap(string, line_width)
  return string if string.length <= line_width
  string[0...line_width] + "\n" + string[line_width..-1]
end
```
Building upon the previous step, the method must also wrap a one word string longer than two line lengths.

```
it "wraps a one word string longer than two line lengths" do
  expect(WordWrap.wrap("hello", 2)).to eq "he\nll\no"
end
```
In order to handle wrapping a string onto a variable number of lines, we can add recursion into our method:

```
def self.wrap(string, line_width)
  return string if string.length <= line_width
  string[0...line_width] + "\n" + wrap(string[line_width..-1], line_width)
end
```

### Back to Dealing With Spaces
The fifth test revisits the idea of wrapping a two word string on its word boundary (i.e. space character).

```
it "wraps on word boundary" do
  expect(WordWrap.wrap("word word", 5)).to eq "word\nword"
end
```
This requires an if-else statement to check for a space character at the line width, in which case it will skip over the space and create a newline.

```
def self.wrap(string, line_width)
  return string if string.length <= line_width
  if string[line_width-1] == " "
    string[0...line_width-1] + "\n" + wrap(string[line_width..-1], line_width)
  else
    string[0...line_width] + "\n" + wrap(string[line_width..-1], line_width)
  end
end
```

The next test documents the case of dealing with a line width value that occurs after the word boundary:

```
it "wraps after word boundary" do
  expect(WordWrap.wrap("word word", 6)).to eq "word\nword"
end
```

In order to successfully handle this case, we need to find the last index of a space character in a substring of characters with a length equal to that of the specified line width. In Java, there is a handy lastIndexOf() method. Ruby does not have a built in method to do this, so we need to come up with a way to implement this ourselves. The Array#index method proves useful for this. This method returns the index of the first item in an array that is equal (==) to the method's argument. If no match is found, the method will return nil. Since this finds the first (not last) match, the string needs to be reversed and the index method can be called with the space character as its argument. Subtracting the result from the line width value will effectively give the last index value. This should be calculated for all substrings that return a value other than nil when the index method is called.

```
def self.wrap(string, line_width)
  return string if string.length <= line_width
  if string[0...line_width].index(" ") != nil
    space_index = (line_width-1) - string[0...line_width].reverse.index(" ")
    string[0...space_index] + "\n" + wrap(string[space_index+1..-1], line_width)
  else
    string[0...line_width] + "\n" + wrap(string[line_width..-1], line_width)
  end
end
```

Now that the cases of wrapping a string at and after the word boundary have been covered, it is time to think about cases of wrapping a string prior to a word boundary. The seventh test documents the method's ability to successfully wrap a string well before its word boundary. This test passes without writing any additional code.

```
it "wraps well before word boundary" do
  expect(WordWrap.wrap("word word", 3)).to eq "wor\nd\nwor\nd"
end
```
### One Final Test

The last case that needs to be dealt with is wrapping a string just prior to the word boundary (i.e. when the space occurs one character after the line width). The eighth and final test looks like this:

```
it "wraps just before word boundary" do
  expect(WordWrap.wrap("word word", 4)).to eq "word\nword"
end
```
Such a case is successfully dealt with by adding an "elsif" statement in our wrap method to check the character right after the specified line width.

```
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

### Wrapping Up (no pun intended)

The wrap method is now fully functional. It still needs some refactoring, which I will walk through in my next blog post. I found this kata to be somewhat challenging at first, but it does a great job illustrating some important concepts. It allows the programmer to experience the benefits of Test Driven Development (TDD) by creating an opportunity to go down the wrong path with the code. In doing such, the programmer is able to experience firsthand what it feels like to write the wrong next test. This exercise taught me how much easier it is to write code in small steps as opposed to trying to solve the majority of the problem at once.

Ultimately, this kata really helped me learn the following: if you find yourself struggling to get a test to pass, there is a good chance you are trying to pass the wrong test.
