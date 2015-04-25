---
layout: post
title: "Creating a Chat Server: Part 2"
date: 2015-04-13 13:54:18 -0500
comments: true
categories:
---

After [creating a working chat server](http://www.lisahamm.com/blog/2015/04/07/creating-a-chat-server/) (able to connect with multiple clients and display user input to all clients), I received some feedback and set out to improve my current code. In this post, I will walk through the steps I took to respond to the feedback I received. <!--more-->

### Thread-safe code

In the first iteration of my chat server, I created a set containing the printWriters of all connected sockets:
```
private static Set<PrintWriter> printWriters = new HashSet<PrintWriter>();
```
While this was working when I manually tested my server, it is not a sound way of accomplishing what I was trying to do. When working with threads, it is important to make sure the code is thread-safe. If multiple threads try to access the same thing at once, issues may arise. In this case, the set should be synchronized in order to guarantee serial access and prevent any unanticipated problems. This way, only one thread can utilize the set a time. Using the `Collections.synchronizedSet` method, I refactored the line of code above as follows:

```
private static Set<PrintWriter> printWriters = Collections.synchronizedSet(new HashSet<PrintWriter>());
```

### Eliminate dependencies

Although the first iteration of the chat server was functioning, its overall design left room for improvement. In its current state, the ChatThread class is fairly coupled to the ChatServer class. It knows about the printWriter HashSet in the server. In order to eliminate this code smell, I began exploring the observer pattern.

### The Observer Pattern

>"Observer is one of those patterns that, once you understand it, you see uses for it everywhere."
>-Uncle Bob in *Agile Software Development: Priniciples, Patterns, and Practices*

The Observer Pattern is typically used for a one-to-many dependency among objects, such that when one object changes, all dependent objects are notified. The independent object is typically referred to as the Concrete Observable and the dependent objects are Concrete Observers. The concrete observable object inherits from an abstract class or interface usually known as the Observable or Subject, which contains a method to register a new observer and a method to notify observers when the observable changes. Concrete Observers implement the Observer interface, which contains an update method (see the UML diagram below).

<img src="{{ root_url }}/images/observer_uml.png" />

So, how could I apply this pattern to a chat server? At first, it seemed simple to me: there were many ChatThread objects that needed to be updated each time a user submitted text. If the chatThread was the observer, then I thought the only object I had left, the server, would be the observable. However, it didn't seem like this should fall into the server's responsibility. After considering different possibilities for this design, I decided to implement a new `ChatSubject` class to take on the role of the Subject:

```java
public class ChatSubject {
    private Vector itsObservers = new Vector();

    protected void notifyObservers(String message) {
        synchronized (itsObservers) {
            Iterator i = itsObservers.iterator();
            while (i.hasNext()) {
                Observer observer = (Observer) i.next();
                observer.update(message);
            }
        }
    }

    public void registerObserver(Observer observer) {
        itsObservers.add(observer);
    }

    public void deregisterObserver(Observer observer) {
        itsObservers.remove(observer);
    }
```

The new `ChatSubject` class would be responsible for registering, deregistering, and notifying observers (the other chat threads in this case). An instance of ChatSubject would be initialized in the ChatServer class and subsequently passed into the ChatThread during its initialization. In the ChatThread's `run()` method, the thread instance is registered with the ChatSubject, and when the user submits input, ChatSubject's `notifyObservers()` method is called. My code can be seen below:

```
public class ChatThread extends Thread implements Observer {
    private Socket clientSocket = null;
    private ChatSubject chatSubject = null;
    private PrintWriter printWriter = null;

    public ChatThread(Socket socket, ChatSubject chatSubject) {
        super("ChatThread");
        this.clientSocket = socket;
        this.chatSubject = chatSubject;
    }

    public void run() {
        try (
                PrintWriter out =
                        new PrintWriter(clientSocket.getOutputStream(), true);
                BufferedReader in = new BufferedReader(
                        new InputStreamReader(clientSocket.getInputStream()))
        ) {
            this.printWriter = out;
            chatSubject.registerObserver(this);
            out.println("Hello, there. Please enter a username to join the chat: ");
            String username = in.readLine();

            String inputLine;
            while ((inputLine = in.readLine()) != null) {
                String message = username + " says: " + inputLine;
                System.out.println(message);
                if(inputLine.equals("Bye"))
                    break;
                chatSubject.notifyObservers(message);
            }
            clientSocket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void update(String message) {
        printWriter.println(message);
    }
}
```

### How has this helped improve the code?

By using a form of the Observer pattern, the code was able to become less coupled and more flexible. The chat thread class is no longer aware of the server class. The Observer pattern also creates flexibility in that Observers can potentially be of any class, as long as they implement the Observer interface. Different types of Observers may need to update in different ways and the Observer's `update()` method allows for this while hiding the implementation details.

The next piece of feedback I addressed was properly shutting down the server. My next post will detail this process.
