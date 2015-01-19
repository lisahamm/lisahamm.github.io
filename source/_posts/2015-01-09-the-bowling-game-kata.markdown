---
layout: post
title: "The Bowling Game Kata"
date: 2015-01-09 15:40:05 -0600
comments: true
categories:
---

Yesterday I sat down with my 8th Light mentor, Doug, and paired on the Bowling Game Kata and wrote it in Ruby. The general concept is to write a program capable of scoring a game of bowling and to use test driven development (TDD) in order to do so. A game of bowling is made up of 10 frames in which a player has two opportunities to knock down 10 pins. The player earns a score equal to the number of pins knocked down in each frame, but also has the opportunity to earn additional points for a spare (whatever is earned in the next roll is added to the 10 points in the frame) and a strike (points earned from the following two rolls are added to the 10 points in the frame).
<!--more-->
Before attempting this kata, I had tried to learn the ropes of TDD on my own, but was having a hard time getting a feel for writing tests for “the next easiest thing.” While pairing with Doug, we went over the Three Rules of TDD which were extremely helpful to me in clarifying the process. The first rule is not to write any production code unless it is to make a failing test pass. The second rule is not to write any more of a test than is sufficient to fail. The third rule is not to write any more production code than is necessary to pass the one failing test.

We started with writing a test for the simplest case of a bowling game–one in which a player rolls all gutter balls. This case would result in a score of zero. After running the test and watching it fail, we added just enough production code to make it pass. The next test was for a simple game of bowling in which a player knocks down one pin each roll. The outcome of this game would be a score of 20. Once again, we ran the test, watched it fail, and added just enough production code to make it pass. We continued along like this for the case of a spare in the first frame, a strike in the first frame, and finally for the cases of a spare and strike in the tenth frame. Along the way, we did some refactoring, but only after we confirmed all tests written up to that point were passing. After finishing the kata with Doug, I felt I had such a better grasp on how to implement TDD and also learned how awesome it is. The simplicity of the code we wrote compared to what I written on my own (without using TDD) was quite a beautiful thing.

After we finished, Doug deleted the program and said it would be a good idea to repeat the kata over and over until I can complete it about 10 minutes. I am a huge believer in “practice makes perfect” and also had a lot of fun with this kata, so I’m excited to keeping working on it until I can meet the 10 minute goal.