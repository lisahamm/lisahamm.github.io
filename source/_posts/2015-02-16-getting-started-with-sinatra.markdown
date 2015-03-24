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

It turned out my code was fairly coupled to the terminal, so I decided to create a new repository from scratch. My mentor suggested I consider using the "Tell, Don't Ask" approach in my new version. This philosophy encourages the telling of an object to do something, rather than asking about its state and then making a decision and doing something.

Once I wrapped up my revised gem, I moved back to my Sinatra app. The [documentation](http://www.sinatrarb.com/) for Sinatra is pretty decent and helped me get started. I created a controller file and saved it as `TicTacToeController.rb`. I added the 'sinatra' gem to my Gemfile and required 'sinatra' in my controller. Next, I ran `bundle install` to install the `sinatra` gem.

The controller file is where you add routes for your application. In Sinatra, a route is an HTTP request paired with a specific URL and an associated block. HTTP, which stands for Hypertext Transfer Protocol, is a network protocol that underpins communication between clients and servers on the internet. There are five verbs commonly used to make requests: GET, POST, PUT, DELETE, and PATCH. A GET request simply asks a server to return a representation of some resource. A POST request is used to submit data to the server. A PUT request is used to create or update data on the server. A DELETE request removes/destroys a resource on the server.

Related posts:
Sinatra - Working with sessions
Sinatra - Testing the controller
Sinatra - User Input Validation and Flash Error Messages




