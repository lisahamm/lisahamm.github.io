---
layout: post
title: "The Code Kata: Practice for Programmers"
date: 2015-01-15
comments: true
categories:
---

I wrote a [blog post](http://www.lisahamm.com/blog/2015/01/09/the-bowling-game-kata/) last week about my experience pairing with my mentor on the Bowling Game Kata. I have continued to work on this kata over the last week and now have more insight into why developers do these.<!--more--> The word “kata” is a Japanese word for “form.” A kata is a karate exercise that is continuously repeated with the intent of making little improvements with each repetition. This concept has been brought into the software development world and is commonly referred to as a Code Kata, which is essentially a practice session for the programmer. Just as an athlete must practice her sport and a musician must practice his instrument, a developer also needs to practice her craft. Each code kata is designed to be a short exercise of which is completed daily for two weeks. The daily practice is designed to ingrain patterns into one’s mind while honing development skills.

I’ve discovered firsthand that a code kata is also a great way for TDD (test driven development) newbies to get a feel for the process. In this post, I’ll go through the process of completing the Bowling Game Kata in Ruby. For a complete overview of the kata, visit [Uncle Bob’s website](http://butunclebob.com/ArticleS.UncleBob.TheBowlingGameKata).

Starting with the simplest case, the first test is written for scoring a gutter game:
```ruby
it "can score a gutter game" do
  bowling_game = BowlingGame.new
  20.times {bowling_game.roll(0)}
  expect(bowling_game.score).to eq 0
end
```
Running rspec at this point results in a failing test:
```
Failure/Error: 20.times {bowling_game.roll(0)}
   NoMethodError:
     undefined method `roll'
```
Now that we have a failing unit test, we can abide by Uncle Bob's first Law of TDD, and write code to enable this test to pass. It is also important to remember the third law of TDD, which states, "You are not allowed to write any more production code than is sufficient to pass the one failing unit test."
```ruby
def roll(pins_down)
  pins_down
end

def score
  0
end
```
Running rspec now results in a passing test, so we can move on to testing the next simplest case: a game in which the player knocks down one pin per roll.
```
it "can score a simple game" do
  bowling_game = BowlingGame.new
  20.times {bowling_game.roll(1)}
  expect(bowling_game.score).to eq 20
end
```
As expected, running this test results in a failure since the code is not currently storing a running total of pins knocked down. Adding the following code will allow this test to pass. Keep in mind, sticking to the rules of TDD requires one to only add enough code to result in a passing test.
```
def initialize
  @total = 0
end

def roll(pins_down)
  @total += pins_down
end

def score
  @total
end
```
Now that both of the tests are passing, it's time for some refactoring. Cleaning up your code and removing code smells is an important process that should be done once your original code is working. By using TDD, the functionality of our code is well documented by our tests. This means we don't have worry that refactoring may or may not break our program. We will know immediately if this is the case when we run our tests after making a change.
```
let(:bowling_game) {BowlingGame.new}

def roll_many(n, pins_down)
  n.times {bowling_game.roll(pins_down)}
end

it "can score a gutter game" do
  roll_many(20, 0)
  expect(bowling_game.score).to eq 0
end

it "can score a simple game" do
  roll_many(20, 1)
  expect(bowling_game.score).to eq 20
end
```
The next text case is for scoring a game with a spare in it.
```
it "can score a game with a spare" do
  2.times {bowling_game.roll(5)}
  roll_many(18, 1)
  expect(bowling_game.score).to eq 29
end
```
In order to score a game with a spare, we will now need a variable to keep track of the number of pins knocked down in each roll. The player will receive 10 points plus the number of pins knocked down during the first roll of the subsequent frame. Since this next step is more of a leap, I am going to refactor the current code further incorporating the spare test. For now, I will place an 'x' at the beginning of the test, which will tell rspec to ignore it.
```
xit "can score a game with a spare" do
  2.times {bowling_game.roll(5)}
  roll_many(18, 1)
  expect(bowling_game.score).to eq 29
end
```
In the BowlingGame class, the @total instance variable can be replaced with another instance variable, @rolls, that is set to equal an empty array. An attr_reader for this variable can also be added to the class. In the roll method, we can add the number of pins knocked down on each roll to the new rolls array. Additionally, now the score method can be refactored to actually implement the scoring.

```
attr_reader :rolls
def initialize
  @rolls = []
end

def roll(pins_down)
  @rolls << pins_down
end

def score
  rolls.reduce(:+)
end
```
After checking to make sure the tests pass at this point, it's time to bring back the spare test by removing the 'x' placed at its beginning.
```
it "can score a game with a spare" do
  2.times {bowling_game.roll(5)}
  roll_many(18, 1)
  expect(bowling_game.score).to eq 29
end
```
In order for this test to pass, the score method needs to check if the pins knocked down in each frame total 10. A while loop can run through each frame by using an index variable to access each roll and then increment its value by 2 so as to move to the next frame.
```
attr_reader :rolls
def initialize
  @rolls = []
end

def roll(pins_down)
  @rolls << pins_down
end

def score
  total = 0
  i = 0
  while i < rolls.length
    if rolls[i] + rolls[i+1] == 10
      total += 10 + rolls[i+2]
    else
      total += rolls[i] + rolls[i+1]
    end
    i += 2
  end
  total
end
```
Now that all of our tests pass, we move on to test the case of scoring a game with a strike.
```
it "can score a game with a strike" do
  bowling_game.roll(10)
  roll_many(18, 1)
  expect(bowling_game.score).to eq 30
end
```
After running rspec and watching this test fail, the score method is modified to account for the case of a strike - when 10 pins are knocked down on the first roll of a frame. Here, the index variable must only increment by a value of 1 in order to move to the next frame (since there is only one roll in a frame with a strike).
```
def score
  total = 0
  i = 0
  while i < rolls.length
    if rolls[i] == 10
      total += 10 + rolls[i+1] + rolls[i+2]
      i += 1
    elsif rolls[i] + rolls[i+1] == 10
      total += 10 + rolls[i+2]
      i += 2
    else
      total += rolls[i] + rolls[i+1]
      i += 2
    end
  end
  total
end
```
Running rspec at this point will show all of our tests currently pass. Since we are in the green, it is a good point to consider refactoring the code. We can add clearly named methods to check for a strike and a spare. We can also add methods to return the bonus points a player earns when scoring both a strike and a spare, and also the sum of the points earned in a frame without a strike or spare.
```
def score
  total = 0
  i = 0
  while i < rolls.length
    if strike?(i)
      total += 10 + strike_bonus(i)
      i += 1
    elsif spare?(i)
      total += 10 + spare_bonus(i)
      i += 2
    else
      total += sum_of_balls_in_frame(i)
      i += 2
    end
  end
  total
end

def strike?(i)
  rolls[i] == 10
end

def spare?(i)
  rolls[i] + rolls[i+1] == 10
end

def strike_bonus(i)
  rolls[i+1] + rolls[i+2]
end

def spare_bonus(i)
  rolls[i+2]
end

def sum_of_balls_in_frame(i)
  rolls[i] + rolls[i+1]
end
```
These explicitly named methods facilitate reading and understanding of the code with ease for anyone who may try to digest it. Following this idea, the spec file can also be refactored to include methods for rolling a spare and rolling a strike.
```
def roll_spare
  2.times {bowling_game.roll(5)}
end

def roll_strike
  bowling_game.roll(10)
end
```
After completing this refactoring, it is now time to tackle the last test case: a perfect game of bowling. This consists of 12 strikes and results in a score of 300 points.
```
it "can score a perfect game" do
  12.times {roll_strike}
  expect(bowling_game.score).to eq 300
end
```
Running rspec now results in the following error:
```
Failure/Error: expect(bowling_game.score).to eq 300
TypeError:
  nil can't be coerced into Fixnum
```
This error message is a common one--it occurs when trying to access an item of an array with an index that reaches further than the length of an array (this results in a nil value). In order to make this test pass, the score method needs a variable to keep track of the frame number. The while loop within this method needs to execute while the frame number is less than 10.
```
def score
  total = 0
  i = 0
  frame = 0
  while frame < 10
    if strike?(i)
      total += 10 + strike_bonus(i)
      i += 1
    elsif spare?(i)
      total += 10 + spare_bonus(i)
      i += 2
    else
      total += sum_of_balls_in_frame(i)
      i += 2
    end
    frame += 1
  end
  total
end
```
You can find the complete kata on my [github](https://github.com/lisahamm/bowling_game_kata). After completing this kata over and over again, I am happy to report that I can now do it in close to 10 minutes and my overall agility on the keyboard has improved immensely.