---
layout: post
title: "Getting Started With Sinatra"
date: 2015-03-23
comments: true
categories:
published: false
---

One of the tasks I have recently worked on during my apprenticeship is building a web application to play a game of tic tac toe. I used [Sinatra](http://www.sinatrarb.com/) to build the web app and utilized my [tic tac toe gem](https://github.com/lisahamm/ruby_ttt) for the game.
<!--more-->

This first involved becoming familiar with Sinatra, which is a domain-specific language (DSL) for building web applications in Ruby. Once I felt comfortable with the basics of Sinatra, my next step was to pull in my tic tac toe code. This code was originally written to run in the terminal, so I needed to review it in order to determine how to incorporate it in a web app. My fingers were crossed that I had written flexible and reusable code the first time around.

Reviewing the code for the purpose of utilizing it with a different interface was actually a great exercise in identifying just how flexible and resusable the code really was.

> Procedural code gets information then makes decisions. Object-oriented code tells objects to do things.
— Alec Sharp, author of *Smalltalk by Example*

It turned out my code was fairly coupled to terminal, so I decided to create a new repository from scratch. My mentor suggested I consider using the "Tell, Don't Ask" approach in my new version. This philosophy encourages the telling of an object to do something, rather than asking about its state and then making a decision and doing something.

Related posts:
Sinatra - Working with sessions
Sinatra - Testing the controller
Sinatra - User Input Validation and Flash Error Messages




