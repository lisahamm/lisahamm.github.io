---
layout: post
title: "Exploring Middleware"
date: 2015-05-24 12:31:07 -0400
comments: true
categories:
---

Middleware is a term used to refer to software that connects various components necessary for an application. This concept can be useful for uncoupling components of an application. This post will explore the usage of middleware within a web application. Using a specific middleware to bridge the gap between an application server and an application/framework provides a layer of abstraction which allows many frameworks to easily configure with many servers.
<!--more-->

### Behind the scenes of sending an HTTP Request

When a user opens up a browser, types in a website's address, and hits the enter key, there is quite a lot happening behind the scenes in order for the requested website to appear on the user's computer. First, when the user enters a URL in a browser, this is actually sending an HTTP request to the corresponding application server. The application server implements the HTTP protocol, which is the universal protocol underpinning the internet (this provides a consistent format for clients and servers to correspond). The application server's role begins with listening for incoming client connection requests. When the server receives and accepts an incoming client connection request, it then reads in the HTTP request sent by the client. The request then needs to be processed in order for the server to send the appropriate HTTP response message to the client. The response sent by the server is responsible for getting the requested website content back to the user's browser.

The above description glosses over the "processing" that occurs between when the raw HTTP request is read by the server and when the server sends the HTTP response to the client. What exactly does this processing entail? The processing typically includes actions such as parsing, logging, and routing the request to the portion of the web app with the knowledge of how to handle the specific request (i.e. what type of content to be rendered by the browser, how to save form data, etc.).

### Middleware

Where and when does middleware come into play? As its name suggests, middleware encompasses the processing of an HTTP request between two server-side points: the I/O channels (where requests are received as input and responses are sent as output) and the app-specific route handling. Middleware is often responsible for parsing the HTTP requests and logging information about these requests.

### Benefits of Middleware

Middleware provides the benefit of modularity among the many components powering web applications on the server-side. Additionally, this also eliminates dependencies between the components. To exemplify, lets examine [Rack](https://github.com/rack/rack), a popular middleware used by Ruby web apps.

### Rack

Rack underlies the Ruby on Rails framework, as well as Sinatra. It provides adapters for many popular web servers and essentially bridges the gap between web servers and applications. A Rack Application must respond to at least one method, which is a #call method that accepts a hash as a variable. Below is a simple example of a Rack app:

```ruby
class RackApp
  def call(env)
    [200, {'Content-Type' => 'text/plain'}, ["Hello world!"]]
  end
end
```

Rack middleware is a class that is initialized with another Rack application and responds to #call(env). Below is a simple example demonstrating the manipulation of the env hash:

```ruby
class RackMiddleware
  def initialize(app)
    @app = app
  end

  def call(env)
    # Set PATH_INFO to always be "/hello_world"
    env['PATH_INFO'] = '/hello_world'
    @app.call(env)
  end
end
```

Suppose a developer is in charge of building and deploying a Ruby application. At the beginning of the project, the developer decides the web application will be a Rack application and Rack middleware will be used with a supported server. The developer reviews all of the supported servers A, B, C, and D and selects server D since it was the fastest server that met the application's needs. Down the road, a new server (server E) is released, which is documented to be twice as fast as Server D (i.e. [Phusion Passenger 5](http://www.rubyraptor.org/)). Given the modularity created by the decision to use Rack, the developer is able to swap out server D with ease, replacing it with Server E. This change would simply require the use of a Rack server handler specific to server E instead of server D. No other changes would be necessary. Additionally, since the application code is completely uncoupled from the server, the application will not experience any side effects from replacing the application server.

The diagram below is from a [Ruby Raptor blog post](http://www.rubyraptor.org/how-we-made-raptor-up-to-4x-faster-than-unicorn-and-up-to-2x-faster-than-puma-torquebox/) and does a great job illustrating this.

<div style="text-align: center"><img src="{{ root_url }}/images/rack.png" /></div>

### Building a server

I am currently implementing my own HTTP server from scratch in Java, which is what led me to learning about middleware. Before I started this project, I had no idea what Rack was doing behind the scenes in Ruby web apps. Writing my own server has helped me gain a great deal of insight into what is really going on "under the hood" in frameworks such as Ruby on Rails and Sinatra. I think this is a great personal project for developers who have a surface understanding of web app development, but might not have taken a deep dive into the HTTP protocol.  "I hear and I forget. I see and I remember. I do and I understand." -Confucius