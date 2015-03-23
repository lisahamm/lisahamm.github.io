---
layout: post
title: "Getting Started With Sinatra"
date: 2015-02-16
comments: true
categories:
---

One of the tasks I have recently worked on during my apprenticeship is building a web application to play a game of tic tac toe. I used [Sinatra](http://www.sinatrarb.com/) to build the web app and utilized my [tic tac toe gem](https://github.com/lisahamm/ruby_ttt) for the game.

This first involved becoming familiar with Sinatra, which is a domain-specific language (DSL) for building web applications in Ruby. Once I felt comfortable with the basics of Sinatra, my next step was to pull in my tic tac toe code. This code was originally written to run in the terminal, so I needed to review it in order to determine how to incorporate it in a web app. My fingers were crossed that I had written flexible and reusable code the first time around.

Reviewing the code for the purpose of utilizing it with a different interface was actually a great exercise in identifying just how flexible and resusable the code really was. It turned out the code was fairly coupled to terminal, so I decided to create a new repository from scratch. My mentor suggested I consider using the "Tell Don't Ask" approach in my new version.

> Procedural code gets information then makes decisions. Object-oriented code tells objects to do things.
— Alec Sharp
