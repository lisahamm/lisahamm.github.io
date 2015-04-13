---
layout: post
title: "Creating a Chat Server"
date: 2015-04-07 16:40:15 -0500
comments: true
categories:
---

After building an [echo server in Java](http://www.lisahamm.com/blog/2015/04/06/creating-an-echo-server/), I moved on to my next project: a chat server. The chat server requires the capability to connect to and handle multiple clients at a time.<!--more--> When one client submits text, the message would need to be sent to all connected clients for viewing. Since the echo server was designed to handle one client connection at a time, my first step was investigating ways a server could handle multiple client connections at a time.

### Concurrent Programming: Threads

There are two options for executing code simultaneously: [threads and processes](https://docs.oracle.com/javase/tutorial/essential/concurrency/procthread.html). Both are self-contained execution environments, but a thread is considered to be more lightweight as compared to a process and takes up less resources during its creation. Java provides the java.lang.Thread class to use for creating Thread objects. Providing code to be executed by a thread can be done in two different ways. One way is through a runnable object, which would define a run method containing the code to be executed in the thread. Alternatively, a subclass of Thread that provides its own run method can be used.

### Implementing a subclass of Thread

I chose to subclass Thread for my chat server. The flow of my code would resemble that of my echo server up to the point of making a successful server client connection. Once the ServerSocket's accept method is executed, a new thread should be created to handle the newly created socket object. This allows the server to handle simultaneous connections with clients. Lets examine the process of implementing the thread subclass starting with:

```java
public static class ChatThread extends Thread {

}
```
The client socket will need to be passed in as an argument in order to initialize the ChatThread object:

```
public static class ChatThread extends Thread {

  private Socket clientSocket = null;

    public ChatThread(Socket socket) {
        super("ChatThread");
        this.clientSocket = socket;
    }
}
```
Next, lets take a look at the run method, which should contain the code to be executed by the thread. First, the thread needs to open readers and writers on the socket's input and output streams:

```
public void run() {
    try (
          PrintWriter out =
                  new PrintWriter(clientSocket.getOutputStream(), true);
          BufferedReader in = new BufferedReader(
                  new InputStreamReader(clientSocket.getInputStream()));
    ) {
      } catch (IOException e) {
                e.printStackTrace();
      }
}
```
At this point, the run method has opened a reader and writer on the clientSocket input and output streams, but it has yet to do anything with this. Thinking back to the echo server, the clientSocket was responsible for reading the data sent to its input stream and then writing this back to its output stream. For a chat server, dealing with input is similar, but the output is different due to needing to update all clients with the output. Here is what I started with:

```
public void run() {
    try (
            PrintWriter out =
                    new PrintWriter(clientSocket.getOutputStream(), true);
            BufferedReader in = new BufferedReader(
                    new InputStreamReader(clientSocket.getInputStream()));
    ) {
        String inputLine;
        while ((inputLine = in.readLine()) != null) {
            System.out.println(inputLine);
            }
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

### Writing to the output streams

The code above handles input from multiple clients, but it does not yet provide the chat functionality of displaying one client's message for all other clients to see. One way to do this is for the ChatServer to save all of the PrintWriters that have been opened on socket output streams:

```
private static Set<PrintWriter> printWriters = new HashSet<PrintWriter>();
```
I used a HashSet to store the open printWriters. In Java, a set is used for a collection of elements with no duplicates. A HashSet is a Set backed by a HashMap. To store the PrintWriters, I added the following line of code to the ChatThread's run method:

```
printWriters.add(out);
```
Then, to actually make use of the set of PrintWriters, I added code to the run method to print the input to all opened PrintWriters:

```
public void run() {
    try (
          PrintWriter out =
                  new PrintWriter(clientSocket.getOutputStream(), true);
          BufferedReader in = new BufferedReader(
                  new InputStreamReader(clientSocket.getInputStream()));
    ) {
        printWriters.add(out);
        String inputLine;
        while ((inputLine = in.readLine()) != null) {
            System.out.println(inputLine);
            for(PrintWriter printWriter : printWriters) {
                printWriter.println(inputLine);
            }
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

### Adding a username

After using [Telnet](http://www.telnet.org/htm/faq.htm) to verify multiple clients could connect and see text submitted by another user, I realized I was missing a key component of a chat: the username. The following code asks the client to input a username immediately after connecting and proceeds to print the username with each of the user's messages:

```
public void run() {
    try (
          PrintWriter out =
                  new PrintWriter(clientSocket.getOutputStream(), true);
          BufferedReader in = new BufferedReader(
                  new InputStreamReader(clientSocket.getInputStream()));
    ) {
        printWriters.add(out);

        out.println("Hello, there. Please enter a username to join the chat: ");
        String username = in.readLine();

        String inputLine;
        while ((inputLine = in.readLine()) != null) {
            System.out.println(inputLine);
            for(PrintWriter printWriter : printWriters) {
                printWriter.println(username + " says: " + inputLine);
            }
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```
### ChatServer class

Incorporating the ChatThread into the ChatServer class produced the following main method:

```
public static void main(String[] args) throws IOException {

        if (args.length != 1) {
            System.err.println("Usage: java EchoServer <port number>");
            System.exit(1);
        }

        int portNumber = Integer.parseInt(args[0]);

        try (ServerSocket serverSocket =
                     new ServerSocket(Integer.parseInt(args[0]))) {
            System.out.println("Server is listening on port: " + portNumber);
            while (true) {
                Socket clientSocket = serverSocket.accept();
                System.out.println("Connection made with " + clientSocket);
                new ChatThread(clientSocket).start();
            }
        } catch (IOException e) {
            System.out.println("Exception caught when trying to listen on port "
                    + portNumber + " or listening for a connection");
            System.out.println(e.getMessage());
        }
    }
```

### Next steps

While the current chat server is functioning, there are still improvements to be made. My next post will build upon this one, going into detail on the next steps.
