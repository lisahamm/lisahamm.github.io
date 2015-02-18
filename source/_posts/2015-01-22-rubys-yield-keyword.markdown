---
layout: post
title: "Ruby's Yield Keyword"
date: 2015-01-22
comments: true
categories:
---

This week I have been working on solving [Project Euler](https://projecteuler.net/) problems with Ruby. Project Euler is a website containing mathematical problems that require a computer to solve them. Many of the problems have common themes such as identifying prime numbers or Fibonacci terms. My mentor recommended placing code pertaining to these common themes in modules so the code can be reused by including the module in the problems that require it.
<!--more-->
One of the modules I created was for handling prime numbers. In order to do this, I had to read up on Ruby's "yield" keyword. The yield keyword invokes the block of code associated with the method in which it is being used. A simple implementation looks like this:

```ruby
def three_times
  yield
  yield
  yield
end

three_times{puts "Hello world"}

#produces:

Hello world
Hello world
Hello world
```

The first method I implemented in my PrimeNumber module was a simple check to see if a given number is prime:
```ruby
def self.prime?(n)
  (2..(n/2)).none? {|i| n % i == 0}
end
```
The next method I implemented was where it got interesting: a method to return all prime numbers up to any given number.
```
def self.prime_numbers_up_to(n)
  i =  2
  while i <= n
    yield i if prime?(i)
    i += 1
  end
end
```
In this method, the while loop begins running with an i value equal to the first prime number, 2. If the value of i is a prime value, the yield keyword will invoke whatever block is given along with the method. Once the associated block has been executed, the method will pick up right where it left off--incrementing i. The loop will continue repeating in this fashion until the value of i becomes greater than the value of the number given as a parameter. Pretty cool!

Similarly, I also wrote a method to return a specific number of prime numbers. This method keeps track of the number of prime numbers it has passed to its associated block and will stop running once this number equals the number passed in as a parameter.

```
def self.prime_numbers_up_to_nth_term(nth_term)
  i = 2
  term_counter = 0
  while term_counter < nth_term
    if prime?(i)
      yield i
      term_counter += 1
    end
    i += 1
  end
end
```
I was able to utilize this method in Project Euler's Problem 7, which reads:

By listing the first six prime numbers: 2, 3, 5, 7, 11, and 13, we can see that the 6th prime is 13.
What is the 10 001st prime number?

In order to solve this problem, I included the PrimeNumber module in my Problem 7 file, and passed 10001 in as the argument. The associated block stores the most recently received prime number, which the method then returns.
```
include PrimeNumber

def self.nth_prime_number(nth_term)
  term = 0
  PrimeNumber.prime_numbers_up_to_nth_term(nth_term) {|n| term = n}
  term
end
```
You can find my Project Euler solutions on my [github](https://github.com/lisahamm/project_euler_tdd).

