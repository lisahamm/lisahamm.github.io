---
layout: post
title: "Factory Pattern"
date: 2015-05-14 16:32:02 -0500
comments: true
categories:
---

The Abstract Factory Pattern entails implementing a class solely responsible for creating concrete objects that conform to an abstract interface.<!--more--> Since concrete classes have the potential to be very volatile, depending upon them often leads to a headache. Modules containing the `new` keyword all over the place smell of rigidity. However, in order to build a program, objects need to be initialized somewhere in the code. By isolating the unstable dependencies, we can uncouple them from the rest of the code and prevent modifications from having unintended effects in other areas of the program. Concrete classes have to be created somewhere in a program, but they do not have to be directly depended upon.The Abstract Factory Pattern helps to accomplish this uncoupling by creating a factory class to initialize concrete classes adhering to a common interface. Using a Factory Implementation class allows for dependencies on abstractions while still creating the necessary concrete classes.

### Handling HTTP Requests

Lets consider the following example. An HTTP server is responsible for receiving and sending messages to clients. Suppose we want to build our own server capable of responding to GET request messages and POST request messages. Both types of requests are handled in a similar manner, but slight differences exist in their respective handling details. We can use a `RequestHandlerFactoryImplementation` to contain the GetRequestHandler and PostRequestHandler initializations.

In order to ensure the Open-Closed Priniciple is followed, a layer of abstraction can be placed upon the RequestHandlerFactoryImplementation by defining a RequestHandlerFactory interface with a "make" method that accepts a request method string.

<div style="text-align: center"><img src="{{ root_url }}/images/factory_pattern.png" /></div>

This way, if the requirements of our server change to include other types of requests (such as PUT and DELETE requests), this change is isolated from the server since the factory interface and the RequestHandler interface will still remain the same.
```
public class RequestHandlerFactory implements HandlerFactory {
    public RequestHandler make(String requestMethod) throws Exception {
        if (requestMethod.equals("GET"))
            return new GetRequestHandler(new GetResponseBuilder());
        else if (requestMethod.equals("POST"))
            return new PostRequestHandler();
        else if (requestMethod.equals("PUT"))
            return new PutRequestHandler();
        else if (requestMethod.equals("OPTIONS"))
            return new OptionsRequestHandler();
        else
            throw new Exception("RequestHandlerFactory cannot create handler for " + requestMethod + " request");
    }
}

public interface HandlerFactory {
    void make(String requestMethod);
}
```
### SOLID
This use of the abstract factory pattern has created code that adheres to both the Dependency Inversion Principle and the Open-Closed Principle.