---
layout: post
title: "The Beauty of Using an Options Hash as an Argument"
date: 2015-02-23
comments: true
categories:

---

What makes code beautiful? I recently listened to an interesting [Ruby Rogues episode](http://devchat.tv/ruby-rogues/what-makes-beautiful-code) on this topic, so it is something I have been mindful of lately in my own work.<!--more-->
As a beginner, I find it helpful to hear answers to this question from programmers with more experience since they have undoubtedly read more code than I have, which generally results in being able to better identify what does and doesn't work. I personally think there are a lot of defining characteristics of beautiful code, many of which I am still learning. In this post, I will share one simple technique I have seen used by more experienced programmers that often leads to more expressive code.

While working on building a web application for the [unbeatable tic tac toe](https://github.com/lisahamm/tic_tac_toe) program I wrote in Ruby some time ago, I came across some problems when trying to use the code with a different interface (I originally wrote it to run in the terminal). My Board class contained an initialize method with two optional arguments:

```ruby
module TicTacToe
  BOARD_SIZE = 3

  class Board
    attr_accessor :cells, :size

    def initialize(size = BOARD_SIZE, cells=nil)
      @size = size
      @cells = cells || Array.new(size*size)
    end

  # rest of code ...
```
This code worked when running the game in the terminal since the board object was simply initialized once at the beginning of the game. However, when trying to use the code with a web interface, I soon realized this code could benefit from some refactoring. Using Sinatra to build the web app, I found myself using sessions to keep track of the board state. I decided to create the board by initializing a Board object with a moves array stored in a session.

```
get '/game' do
  @player_mark = session[:mark]
  @board = Board.new(session[:moves])
  session[:moves] = @board.symbols_to_array
  erb :board
end
```
I also decided to use Ruby's Struct class to create Cell objects and wrote a `cellify` method to format cell data passed in as an argument:

```
 def initialize(size = BOARD_SIZE, cells=nil)
  @size = size
  @cells = cellify(cells || Array.new(size*size))
end

Cell = Struct.new(:symbol, :index)

def cellify(data)
  data.each_with_index.map {|symbol, index| Cell.new(symbol, index)}
end
```
### The Problem
Attempting to run the code at this point resulted in an error message since the board's `initialize` method was expecting a board size parameter to be passed in as its first argument. In order to fix this, the caller would have to pass the cells as the second argument. However, that would force the caller to also have to pass something for the first argument, which defeats the point of having an optional parameter. So how could the caller pass in only the arguments which they care about and ignore the others, having them safely defaulted?


### A Better Way
Instead of passing in multiple arguments, a common practice is to pass in one argument, which is a hash containing arguments as key-value pairs. Default values can be provided by a method that returns a hash and then is subsequently merged with the actual arguments.


```
def initialize(options)
  options = defaults.merge(options)
  @size = options[:size]
  @cells = cellify(options[:cells] || Array.new(size*size) {nil})
end

def defaults
  {size: BOARD_SIZE, cells: nil}
end
```

Doing this allows the caller to then pass in values to overide the defaults as needed. The caller does not have to pass certain arguments in every time an object is initialized when using the default values. This technique also makes the argument order irrelevant, which becomes particularly important when passing a large number of arguments to a method.

### Beautiful Code
Overall, this simple technique lends itself to creating more flexible, reusable, and in my opinion, beautiful code. Incorporating this into the app's server file also seemed to facilitate readability since the key is now serving as somewhat of a label for the argument being passed to board:

```
get '/game' do
  @player_mark = session[:mark]
  @board = Board.new(cells: session[:moves])
  session[:moves] = @board.symbols_to_array
  erb :board
end
```

Pretty neat! I will definitely be utilizing this technique in my code from now on.