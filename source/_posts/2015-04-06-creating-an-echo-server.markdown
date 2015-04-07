---
layout: post
title: "Creating an Echo Server"
date: 2015-04-06 15:03:52 -0500
comments: true
categories:
---

One of the projects 8th Light has its residents complete is building a web server. This week, I started working on a couple of programs to ease into the world of web servers.<!--more--> The first is a simple Echo Server program that will say "Hello" upon connection and repeat all of the messages sent to it by the client. Subsequently, I will build upon the program to create a chat server that allows multiple connections. When one person enters text, everyone connected to the server will see it.

I have not written any Java code for some time now, so I began by downloading [IntelliJ](https://www.jetbrains.com/idea/download/) which seems to be the Java IDE of choice at 8th Light. Next, I began reading about the Transmission Control Protocl (TCP) and sockets. [Oracle's Java tutorials](https://docs.oracle.com/javase/tutorial/networking/sockets/index.html) proved to be a useful resource in learning about both. The rest of this post is an overview of what I learned from the tutorial, and my experience creating, running, and connecting to an Echo Server.

### TCP

TCP is a network communication protocol that provides a reliable channel for a client and a server to communicate with eachother. Client-server applications on the internet communicate over TCP by establishing a connection between the client and server and binding a socket to each end of the connection. The server and the client read from and write to their respective socket.

### What is a socket?

A socket is defined as "one endpoint of a two-way communication link between two programs running on the network. A socket is bound to a port number so that the TCP layer can identify the application that data is destined to be sent to." [(Oracle Documentation)](https://docs.oracle.com/javase/tutorial/networking/sockets/definition.html). The server continuously listens to its socket for connection requests. When a client wants to make a request to the server, it binds to a local port number to identify itself, and then uses the host name and port number of the server's socket to request a connection. When a connection is established, the server binds a new socket to the same local port so it can listen for incoming requests as well as communicate with the connected client. Every connection can be uniquely identified by its sockets, since these endpoints are a combination of an IP address and port number.

### Creating a socket using Java

A Socket class is included in the java.net package and allows Java programs to communicate in a platform-independent way, by hiding system-specific details from the program. A ServerSocket class is also included and allows servers to listen for requests and make connections.

### Building an EchoServer

The EchoServer program will take a port number as a commandline argument, which is used as the location for binding the ServerSocket object that will listen for connection requests from a client. Let's take a look at the start of the EchoServer program:

```java
public class EchoServer {
  public static void main(String[] args) throws IOException {

    if (args.length != 1) {
      System.err.println("Usage: java EchoServer <port number>");
      System.exit(1);
    }

    int portNumber = Integer.parseInt(args[0]);
  }
}
```

The main method will return an error if it is not given exactly one argument to use as the port number. If exactly one argument is passed in, the program will set its `portNumber` variable to the argument value.

Here are the next few steps:
(1) Create the server socket to bind to the server's specified port.
(2) Accept the connection request from the client.
(3) Open a `PrintWriter` on the socket's output stream.
(4) Open a `BufferedReader` on the socket's input stream.

These steps can be completed in a try-with-resources statement to ensure the sockets, PrintWriter, and BufferedReader are closed at the end of the statement.

```
try (
      ServerSocket serverSocket =
            new ServerSocket(Integer.parseInt(args[0]));
      Socket clientSocket = serverSocket.accept();
      PrintWriter out =
            new PrintWriter(clientSocket.getOutputStream(), true);
      BufferedReader in = new BufferedReader(
            new InputStreamReader(clientSocket.getInputStream()));
) {
    out.println("Hello");
    String inputLine;
    while ((inputLine = in.readLine()) != null) {
        out.println(inputLine);
    }
} catch (IOException e) {
    System.out.println("Exception caught when trying to listen on port "
        + portNumber + " or listening for a connection");
    System.out.println(e.getMessage());
}
```

The code above declares instances of ServerSocket, Socket, PrintWriter, and BufferedReader as resources. If the ServerSocket, which listens for connection requests from clients, is successfully created and bound to the specified port number, the program moves on to this line of code: `Socket clientSocket = serverSocket.accept();`. The `accept` method returns a new Socket object, with its remote address and port set to that of the client, bound to the specified port number of the server. This allows the server to communicate with the client using this new socket, while the ServerSocket continues to listen for connection requests.

The program writes "Hello" to the output stream, reads input from the client, and then writes the input to its output stream (creating the 'echo'). The ServerSocket constructor will throw an exception if it is unable to be created on the specified port, so the catch statement prints out the corresponding message when this is the case.

Putting this all together produces the following program:

```
import java.net.*;
import java.io.*;

public class EchoServer {
    public static void main(String[] args) throws IOException {

        if (args.length != 1) {
            System.err.println("Usage: java EchoServer <port number>");
            System.exit(1);
        }

        int portNumber = Integer.parseInt(args[0]);

        try (
                ServerSocket serverSocket =
                        new ServerSocket(Integer.parseInt(args[0]));
                Socket clientSocket = serverSocket.accept();
                PrintWriter out =
                        new PrintWriter(clientSocket.getOutputStream(), true);
                BufferedReader in = new BufferedReader(
                        new InputStreamReader(clientSocket.getInputStream()));
        ) {
            out.println("Hello");
            String inputLine;
            while ((inputLine = in.readLine()) != null) {
                out.println(inputLine);
            }
        } catch (IOException e) {
            System.out.println("Exception caught when trying to listen on port "
                    + portNumber + " or listening for a connection");
            System.out.println(e.getMessage());
        }
    }
}
```

### Connecting with the server

Once the EchoServer class has been created, it is time to try running the server. Remember, the program requires the port number commandline argument in order to run. In IntelliJ, you can set program arguments by going to the "Run" menu, clicking on "Edit Configurations," and filling in the "Program Arguments" field inside the "Configuration" tab. If the port number argument is provided in this way, the server will start running when the program is run through clicking IntelliJ's "Run" button.

[Telnet](http://www.telnet.org/), a network protocol and also an application that uses the protocol, can be used to connect to the server through the terminal. In the terminal, type the following to connect to the server:

```
$ telnet servername-or-ip port-number
```

If the connection is established, a message will appear in the terminal stating something like, "Connected to host-name." With this particular server, a "Hello" message should also be printed in the terminal. To see the echo in action, type any message, hit enter, and the same message should be returned and printed in the terminal.

### Summary

TCP provides reliable communication between a server and client. The server has a designated port with a socket listening for connection requests from clients. When the server-client connection is established successfully, both the server and the client communicate through their respective sockets by reading from the input stream and writing to the output stream.

