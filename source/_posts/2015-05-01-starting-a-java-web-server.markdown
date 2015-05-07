---
layout: post
title: "Starting a Java Web Server"
date: 2015-05-01 10:14:00 -0500
comments: true
categories:
---

After building an echo server and a chat server in Java, I moved on to building a HTTP (Hypertext Transfer Protocol) server.<!--more--> I started by getting a suite of acceptance tests for the server running on my computer. I was surprisingly excited for this for a couple of reasons. First, I have read a lot about acceptance tests in Uncle Bob's books, [Clean Coder](http://www.amazon.com/The-Clean-Coder-Professional-Programmers/dp/0137081073) and [Agile Software Development](http://www.amazon.com/Software-Development-Principles-Patterns-Practices/dp/0135974445), but I have yet to actually work with these. Secondly, I have heard other apprentices discuss the acceptance tests during our daily standup meeting, and now I am able to join the conversation.

### Acceptance Tests

Acceptance tests provide documentation of the features of a system. These tests are usually written by the customer and serve to verify the customer's requirements are being met. While unit tests provide coverage for small elements of the internals of the system, acceptance tests ensure the system is working properly as a whole.

At 8th Light, apprentices use a [FitNesse](http://fitnesse.org/) test suite, [Cob Spec](https://github.com/8thlight/cob_spec), to validate a server adheres to the HTTP specifications. I followed the instructions in Cob Spec's readme, which was pretty straightforward, but I did run into an error when I ran `$ mvn package`. The build was failing but I was not quite sure why. Turns out, it was because Cob Spec was written for Java 7, but I am using Java 8. In order to fix this issue, I downloaded Java 7, added it to my PATH, and built the project by executing the following:

```
$ export JAVA_HOME=/usr/java/jdk1.7.0_80/bin/java
$ export PATH=$PATH:/usr/java/jdk1.7.0_80/bin
$ mvn package
```
After doing this, I closed out of the terminal window I was working in order to switch back to Java 8 for development. At this point, I was able to run the test suite, though all the tests were failing since I had yet to write any code for my web server. The next step would entail writing code to pass a single acceptance test.

### What is HTTP?

Before diving into the first test, it helps to have an understanding of HTTP. HTTP is a request/response protocol used by the World-Wide Web global initiative. The generic, stateless, application-level protocol allows for systems to be built independently of the data being transferred through request and response messages. The official protocol can be found [here](http://tools.ietf.org/html/rfc2616#section-1).

### HTTP Messages

HTTP messages are sent in the form of requests and responses. The general format looks like this:
```
Start-line CRLF
Message-header CRLF
CRLF
[message-body]
```
It should be noted there can be several message-header lines in an HTTP message. `CRLF` indicates a new line (it stands for carriage return and line feed). The CRLFs become important when interpreting a message because they indicate where the various components of a message start and end. Let's break this down further by exploring the request and response messages separately.

### HTTP Request
A request follows the message format above. The first line of the request (the start-line) is specifically referred to as the request-line, and is made up of the following components:

```
Method Request-URI HTTP-Version CRLF
```
The method is the action to be performed on a specified resource. The possible methods are GET, POST, PUT, DELETE, OPTIONS, HEAD, TRACE, and CONNECT. A resource is a chunk of information that can be identified by a Uniform Resource Identifier (URI). In the request-line, the Request-URI identifies the resource on which to apply the given method. A GET request simply asks a server to return a representation of some resource. A POST request is used to submit data to the server. A PUT request is used to create or update data on the server. A DELETE request removes/destroys a resource on the server. The HTTP-Version in the request-line specifies the version of the protocol being used. The following is an example of an actual request line:
```
GET / HTTP/1.1
```
### HTTP Response

Response messages also follow the general message format. The message start-line is specifically referred to as the status-line. The breakdown of the status-line components is as follows:
```
HTTP-Version Status-code Reason-phrase CRLF
```
Again, the HTTP-Version is the specific protocol version being used. The status-code is a three-digit integer that identifies a success or error in handling the request. The first digit of the status-code must be a number 1 through 5 which indicates the category of response. The protocol defines the categories as follows:

-1xx: Informational - Request received, continuing process<br>
-2xx: Success - The action was successfully received, understood, and accepted<br>
-3xx: Redirection - Further action must be taken in order to complete the request<br>
-4xx: Client Error - The request contains bad syntax or cannot be fulfilled<br>
-5xx: Server Error - The server failed to fulfill an apparently valid request<br>

The reason-phrase is a brief text description of the status-code intended for the human user. An example of an actual response-line is as follows:
```
HTTP/1.1 200 OK
```

### Next steps

After becoming more familiar with the HTTP protocol, I felt prepared to tackle my first acceptance test. Looking for the easiest test, I chose to start with a simple GET request that would return a [response code](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) of 200 (which indicates the request succeeded).


### Helpful Resources
-[HTTP Made Really Easy](http://www.jmarshall.com/easy/http/#resources)<br>
-[Official HTTP Protocol](http://tools.ietf.org/html/rfc2616#section-1)<br>
-[Java Tutorial: Networking](http://docs.oracle.com/javase/tutorial/networking/index.html)
